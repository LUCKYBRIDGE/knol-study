# 같은 문제를 서로 다르게 푼 방식 비교

검토일: 2026-11-08

이 문서는 5개 제품의 기능 수를 비교하지 않는다. **동일한 제품 문제를 서로 어떤 아키텍처로 해결했는지**를 비교한다.

# 1. 복잡한 사용자 입력

| 제품 | 해법 | 장점 | 비용 |
|---|---|---|---|
| tldraw | 계층형 StateNode 상태 머신 | 중간 상태·취소·전이가 명확 | 개념/클래스 수 증가 |
| Excalidraw | App 기능 모듈 + ActionManager + capture policy | UI/shortcut/API 명령 통합 | App 중심 구조가 여전히 큼 |
| H5P | schema 기반 generic editor + custom widget | 활동별 폼 반복 구현 감소 | 특수 UX는 schema만으로 부족 |
| Twine | Passage 직접 편집 + Story Map | 초보자에게 구조가 즉시 보임 | 고급 runtime 문법은 format별 |
| Artsteps | domain-oriented spatial editor | 3D engine 개념을 숨김 | 3D 조작 자체 난도는 남음 |

## 선택 규칙

- gesture 단계가 많음 → tldraw식 state machine
- toolbar/shortcut/API 명령 통합 → Excalidraw식 Action
- 반복 설정 폼 → H5P semantics
- 관계 편집 → Twine식 visual map
- 전문 기술을 비전문가에게 → Artsteps식 domain vocabulary

# 2. 상태 분리

## tldraw
```text
document / session / presence
+ session field preservation
```

가장 엄격한 lifecycle 모델.

## Excalidraw
```text
elements / AppState / files
+
browser / export / server
+
durable / ephemeral
```

가장 다차원적인 정책 모델.

## H5P
```text
content instance
library code
editor state
runtime learner state
host state
```

플랫폼 경계 중심.

## Twine
Story/Passage 안에 일부 UI state를 두고 persistence filter로 제거.

## Artsteps
내부 확인 불가. 제품 수준에서는 authoring/viewer/social domain이 구분됨.

# 3. 저장 문제

| 제품 | 핵심 철학 |
|---|---|
| tldraw | versioned Store를 그대로 persistence/sync의 기반으로 |
| Excalidraw | 데이터 종류에 맞게 localStorage/IDB/server를 선택 |
| H5P | host가 persistence 구현, Core는 contract 제공 |
| Twine | web/Electron 저장 adapter를 분리 |
| Artsteps | hosted publication 중심, 내부 구현 Unknown |

### 교훈
"어떤 DB를 쓸까?" 전에:
1. 무엇을 저장하는가
2. 얼마나 오래 살아야 하는가
3. 다른 기기와 공유되는가
4. binary인가
를 결정한다.

# 4. 실시간 공동 작업

## tldraw
server-authoritative room + network diff + client rebase.

강점:
- 권한
- schema/version
- invalid record
- authoritative consistency

## Excalidraw
socket realtime + client reconcile + Firebase durable storage + E2EE.

강점:
- client-side encrypted room
- local-first와 hosted collaboration 연결

## Twine
core product의 중심 문제가 아님.

## H5P
content authoring realtime collaboration은 core abstraction의 중심이 아님.

## Artsteps
social/avatar는 확인되나 authoring collaboration internals Unknown.

### 교훈
협업이 필요하지 않은 서비스에 sync architecture를 미리 넣지 않는다.

# 5. 확장성

| 제품 | 확장 방식 | 특징 |
|---|---|---|
| tldraw | ShapeUtil/BindingUtil/Tool/UI/record/adapter | application framework형 |
| Excalidraw | React component props/API + source-level actions | embeddable editor형 |
| H5P | library.json + semantics + dependency | plugin ecosystem형 |
| Twine | Story Format + editor extension | runtime 교체형 |
| Artsteps | creator 기능 중심, developer extension 확인 불가 | hosted product형 |

# 6. 사용자 난이도 제어

## tldraw
기본 UI는 풍부하지만 SDK는 매우 복잡. 최종 app 제작자가 UI를 재구성할 수 있음.

## Excalidraw
처음에는 핵심 도구가 바로 보이고 선택 상태에 따라 속성을 노출.

## H5P
Content type 선택 후 schema-generated authoring.

문제: 콘텐츠 타입 수가 많으면 선택 단계가 복잡해짐.

## Twine
기본 link부터 시작하고 변수/코드는 나중.

## Artsteps
3D primitive 대신 전시 domain language.

# 7. 관계가 복잡할 때

- tldraw: Binding records
- Excalidraw: boundElements / arrows / frames / groups
- H5P: dependency graph / nested libraries
- Twine: passage graph
- Artsteps/Metasteps: guided path / portal graph

같은 "관계"라도 목적이 다르므로 하나의 universal graph engine으로 통일할 이유는 없다.

# 8. Undo

## tldraw
Editor/Store history manager와 transaction/diff 체계.

## Excalidraw
StoreDelta → inverse HistoryDelta. remote/ephemeral 의미까지 고려.

## Twine
undoable stories wrapper + passage text editor undo를 구분.

## H5P
저작 editor의 일반적인 field 편집이며 core architecture의 핵심 차별점은 아님.

# 9. Portability

| 제품 | portable artifact |
|---|---|
| tldraw | JSON-compatible Store snapshot/exports |
| Excalidraw | open .excalidraw JSON + PNG/SVG |
| H5P | .h5p content + library package |
| Twine | standalone HTML / Twee / story data |
| Artsteps | hosted share/embed 중심 |

### 가장 강한 portability 모델
- 단순 작품 데이터: Excalidraw
- 실행 환경까지 포함한 교육 콘텐츠: H5P
- 최종 독립 실행물: Twine HTML

# 10. 오류를 푸는 위치

## tldraw
schema/store/protocol/server authorization.

## Excalidraw
restore/reconcile/store/history/local file status.

## H5P
package validator + semantics validator + whitelist + permission.

## Twine
Story Map visual diagnostics + startup repair + storage warning.

## Artsteps
내부 확인 불가.

### 핵심 차이

```text
tldraw/H5P
  경계에서 강하게 거부

Twine
  작성자가 시각적으로 발견

Excalidraw
  변경/복원 과정에서 수렴·복구
```

# 11. 테스트 철학

- tldraw → distributed state invariants/fuzz/migrations
- Excalidraw → interaction/regression/reconciliation breadth
- Twine → UI semantics/accessibility
- H5P → contract/validation 중심 구조, 주요 repo에서 자동 test tree는 상대적으로 덜 드러남
- Artsteps → 비공개

# 12. Knol에서 조합할 때의 원칙

모든 최선의 구조를 합치지 않는다.

### 편집기 core
tldraw:
- lifecycle
- schema
- state machine

### 사용자 친화적 editor
Excalidraw:
- Action
- contextual UI
- persistence policy

### 교육 활동 생태계
H5P:
- manifest
- semantics
- author/player split

### 이야기 저작
Twine:
- Story Map
- auto-linked nodes
- visual diagnostics

### 전시
Artsteps:
- artifact
- viewpoint
- guided path
- publication

**문제별로 가장 맞는 구조만 골라야 한다.**
