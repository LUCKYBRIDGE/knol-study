# tldraw — 복잡한 편집기 문제를 푸는 방식

검토일: 2026-11-08  
소스 기준: 공식 `tldraw/tldraw` main, editor/sync package 5.6.0

# 1. tldraw이 실제로 푸는 어려운 문제

겉으로는 화이트보드지만 기술적으로는 다음 문제를 동시에 풀어야 한다.

- 수천 개의 객체가 있는 무한 캔버스
- 선택·이동·리사이즈·회전·그리기·텍스트 등 다단계 입력
- 객체별 서로 다른 geometry와 interaction
- undo/redo
- 파일·이미지 자산
- 자동 저장
- 오래된 문서 migration
- 여러 탭
- 실시간 공동 편집
- 사용자 presence
- network interruption
- 서로 다른 client schema version

tldraw은 이를 거대한 하나의 React state로 풀지 않고 **문제의 수명주기와 책임별로 층을 나눈다.**

# 2. 가장 중요한 해법: "UI가 데이터를 직접 소유하지 않는다"

```text
User Input
   ↓
Tool State Machine
   ↓
Editor API
   ↓
Typed Record Store
   ↓
Reactive Signals
   ↓
Render / Persistence / Sync
```

UI 버튼과 canvas gesture는 Store를 직접 조작하기보다 Editor라는 명령 계층을 통해 의미 있는 작업을 수행한다.

공식 문서도 Editor를 캔버스 조작의 주 인터페이스로 설명한다.

## 왜 유용한가

React component가:
- shape 배열
- 선택 상태
- undo
- sync
- validation

을 동시에 책임지지 않는다.

따라서 UI 교체와 데이터 시스템 변경의 결합도가 낮다.

# 3. 다단계 입력은 State Machine으로 푼다

`StateNode`는 계층형 상태 머신이다.

예:

```text
Select Tool
├─ idle
├─ pointing_shape
├─ translating
├─ pointing_resize_handle
├─ resizing
├─ rotating
└─ editing_shape
```

입력 event는:

```text
Root State
  ↓
Active Tool
  ↓
Active Child State
```

로 전달된다.

각 state는:
- onPointerDown
- onPointerMove
- onPointerUp
- onKeyDown
- onCancel
- onComplete
- onEnter
- onExit

등을 처리한다.

## 이 방식이 푸는 문제

드래그 중 Escape, 리사이즈 중 pointer-up, 도구 전환 중 interruption 같은 **중간 상태**를 boolean 여러 개로 표현하면 조합 폭발이 일어난다.

State machine은:

```text
isDragging
isResizing
isRotating
isEditing
...
```

의 독립 boolean 조합 대신 **현재 가능한 상태 하나와 전이 규칙**을 만든다.

## Knol 관점

테셀레이션 편집처럼:
- 변 선택
- 자르기
- 이동/회전/반사
- 미리보기
- 확정

이 연속 행동이 복잡해지면 단순 event handler보다 state machine 접근이 유효하다.

# 4. Shape 다양성은 ShapeUtil polymorphism으로 푼다

각 shape type은 데이터만 다른 것이 아니다.

`ShapeUtil`이 정의하는 책임:
- props validation
- props migration
- default props
- geometry
- component/render
- indicator
- snapping
- binding 가능성
- asset에서 shape 생성
- accessibility name
- layout behavior

즉:

```text
Shape Record = 저장할 데이터
ShapeUtil    = 해당 타입의 행동/geometry/render 규칙
```

으로 나뉜다.

## 왜 중요한가

Editor core에:

```text
if rectangle ...
else if arrow ...
else if image ...
```

가 계속 늘어나지 않는다.

새 shape type은 확장 지점으로 들어간다.

# 5. 복잡한 Editor는 Manager들로 기능을 수평 분해

실제 manager 디렉터리:

- ClickManager
- CollaboratorsManager
- EdgeScrollManager
- FocusManager
- FontManager
- HistoryManager
- InputsManager
- PerformanceManager
- ScribbleManager
- SnapManager
- SpatialIndexManager
- TextManager
- ThemeManager
- TickManager
- UserPreferencesManager

Editor 하나가 public facade 역할을 하지만 내부 기능은 manager에 위임한다.

## 패턴

```text
Large Facade API
      ↓
Specialized Managers
      ↓
Store / Geometry / State
```

사용자에게는 한 Editor API를 주되 구현은 세분화한다.

# 6. 상태 문제는 "타입별 수명주기"로 푼다

Store Record scope:

```text
document
  저장 + 공유 + sync

session
  현재 tab/editor
  sync하지 않음

presence
  다른 사용자에게 실시간 전달
  장기 저장하지 않음
```

이게 1차 분리다.

그 뒤 `TLInstance` 내부에서도 field별로:
- 다음 session에도 보존
- load 시 reset

을 다시 구분한다.

따라서 실제 해법은:

```text
Record lifecycle
       +
Field lifecycle
```

이다.

# 7. 잘못된 데이터는 Store에 들어오기 전에 Schema로 막는다

RecordType은:
- ID 규칙
- validator
- scope
- default props
- migration

을 가진다.

`StoreSchema`는 여러 record type을 하나의 문서 schema로 조합한다.

## 효과

잘못된 shape를 renderer에서 뒤늦게 처리하는 대신 **데이터 경계에서 거부**한다.

# 8. 오래된 문서는 Migration을 일급 개념으로 다룬다

Document, shape props, presence 등 record마다 migration sequence가 존재한다.

특히 sync test는 구버전 client와 신버전 server 사이:
- upgrade
- downgrade
- 지원할 수 없는 새 record type
까지 검사한다.

지원 불가능한 stale client는 `CLIENT_TOO_OLD`로 명시적으로 거절할 수 있다.

## 설계 교훈

schema migration은 단순 local file 문제만이 아니다.

협업에서는:

```text
Client schema
↕
Server schema
↕
Other client schema
```

호환성 문제다.

# 9. 로컬 저장은 편의 기능, Sync는 별도 시스템

## Local persistence

`persistenceKey`:
- IndexedDB 자동 저장
- uploaded asset 저장
- 같은 key의 browser tab 간 sync

목표: **reload/crash/tab recovery**.

## Multiplayer

별도 `@tldraw/sync` / `sync-core`.

목표:
- 여러 사용자
- authoritative shared state
- network failure
- conflict

둘은 같은 문제가 아니다.

# 10. 실시간 공동편집은 CRDT 만능 접근 대신 Server-authoritative rebase

공식 sync 문서와 source의 핵심:

```text
Client local optimistic edit
        ↓
network diff
        ↓
authoritative TLSocketRoom
        ↓
validation / authorization
        ↓
server document clock
        ↓
diff back to clients
        ↓
client rebase/merge
```

서버가 authoritative copy를 가진다.

## 장점
- 권한 검사 위치가 명확
- invalid record 차단
- server-side record rewrite 가능
- 일관된 document clock

## 비용
- 각 room에 authoritative server instance가 필요
- network protocol/version 관리 필요
- offline peer-to-peer 모델과는 다름

# 11. "방 하나 = authoritative room 하나"로 overwrite 문제를 푼다

공식 문서는 동일 document에 전역적으로 TLSocketRoom이 하나여야 한다고 명시한다.

Cloudflare 권장 구조:
- room → Durable Object
- document → SQLite
- asset → R2

```text
Room ID
  ↓
one Durable Object
  ↓
one TLSocketRoom
  ↓
SQLite state
```

서버 인스턴스가 여러 개에서 같은 문서를 독립적으로 수정하는 split-brain 문제를 피한다.

# 12. binary asset는 document와 분리

`TLAsset` record:
- metadata/reference

`TLAssetStore`:
- upload
- resolve
- remove

실제 blob 저장:
- memory
- IndexedDB
- R2/S3 등

이렇게 JSON document와 large binary의 lifecycle을 분리한다.

# 13. 오류는 taxonomy로 관리

sync fatal reasons:
- NOT_FOUND
- FORBIDDEN
- NOT_AUTHENTICATED
- UNKNOWN_ERROR
- CLIENT_TOO_OLD
- SERVER_TOO_OLD
- INVALID_RECORD
- RATE_LIMITED
- ROOM_FULL
- MESSAGE_TOO_LARGE

## 중요한 이유

`sync failed` 한 종류로 처리하면 사용자 복구 행동을 안내할 수 없다.

예:
- CLIENT_TOO_OLD → 새로고침/업데이트
- FORBIDDEN → 권한 안내
- MESSAGE_TOO_LARGE → 콘텐츠/업로드 제한
- RATE_LIMITED → 재시도 정책

# 14. 테스트가 보여주는 tldraw의 실제 두려움

## sync fuzz

`syncFuzz.test.ts`는:
- 최대 여러 peer
- random operation
- random disconnect/reconnect
- server/client message 순서 변화
- undo/redo
- presence
를 반복하고 마지막에 모든 document store가 동일한지 확인한다.

즉 단순 happy path가 아니라 **분산 상태가 결국 수렴하는가**를 테스트한다.

## upgrade/downgrade

서로 다른 schema version client를 같은 room에 붙여:
- old → new migration
- new → old downgrade
- 지원 불가 schema 거절
을 검증한다.

## editor tests

manager, StateNode, geometry, LocalIndexedDb, local sync 등을 각각 테스트한다.

# 15. tldraw이 택한 핵심 trade-off

### 얻는 것
- 확장성
- 데이터 안정성
- migration
- 협업 견고성
- custom shape/tool
- 테스트 가능성

### 지불하는 것
- 높은 개념 수
- 큰 API
- 많은 package
- state/store/schema/editor/sync 학습 비용
- 작은 서비스에는 과한 구조

# 16. 가장 재사용 가치가 큰 원리

1. **UI가 canonical data를 직접 소유하지 않는다.**
2. **다단계 interaction은 state machine으로 모델링한다.**
3. **데이터와 behavior를 Record + Util로 분리한다.**
4. **Editor는 facade, 복잡 기능은 manager로 분해한다.**
5. **document/session/presence를 lifecycle로 분리한다.**
6. **asset metadata와 blob을 분리한다.**
7. **migration은 파일뿐 아니라 client/server compatibility 문제로 본다.**
8. **협업은 권한·버전·용량 오류를 명시적인 protocol error로 다룬다.**
9. **randomized/fuzz test로 동시성 회귀를 찾는다.**
