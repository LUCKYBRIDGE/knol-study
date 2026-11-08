# Excalidraw — 단순한 UX와 강한 상태 시스템을 공존시키는 방식

검토일: 2026-11-08  
소스 기준: 공식 `excalidraw/excalidraw` master, npm package 0.18.0

# 1. Excalidraw의 핵심 문제

제품은 매우 단순해 보이지만 내부 문제는 복잡하다.

- 누구나 바로 그릴 수 있어야 함
- infinite canvas
- shape/arrow/text/image/freehand
- 낮은 UI 학습비
- undo/redo
- open file format
- local-first
- offline/PWA
- binary image
- realtime collaboration
- E2EE
- 여러 client가 동시에 object 순서를 변경
- embed library로도 사용 가능

Excalidraw의 핵심 전략은 **core editor와 hosted application의 복잡도를 분리하고, 내부에서는 상태·변경·저장 목적을 각각 다른 축으로 분류하는 것**이다.

# 2. 가장 중요한 제품 경계: Editor Package와 Hosted App 분리

```text
@excalidraw/excalidraw
  canvas/editor/element/UI core
          ↓ embed

excalidraw.com
  local persistence
  PWA
  Firebase
  Socket collaboration
  share links
```

README가 이 차이를 명시한다.

## 왜 좋은가

embed 사용자는 Firebase나 hosted collaboration까지 떠안지 않는다.

제품 서버 기능을 SDK core에서 제거해:
- package portability
- bundle 독립성
- self integration
을 높인다.

# 3. UI 명령은 ActionManager로 통합

`ActionManager`는 action registry를 가진다.

Action은:
- name
- keyboard trigger
- perform
- predicate
- panel component
- tracking policy
등을 묶는다.

명령이:
- keyboard
- UI
- API

어디서 시작되더라도 같은 `perform()` 경로를 사용할 수 있다.

```text
Keyboard ─┐
UI Panel ─┼→ ActionManager → Action.perform → updater
API ──────┘
```

## 이 방식이 푸는 문제

UI 버튼용 로직과 shortcut용 로직이 따로 생겨 behavior가 어긋나는 것을 줄인다.

또 view mode/read-only/navigation 같은 정책을 action 실행 경계에서 통제한다.

# 4. 거대한 App은 최근 기능별 객체로 분해된다

실제 components에는:
- App.selection
- App.selectionTool
- App.text
- App.textTool
- App.pan
- App.viewport
- App.wheel
- App.clipboard
- App.duplicate
- App.drawshape
- App.bucketFill
- App.flowchart
- App.toolDrag
- App.cursor
등이 존재한다.

즉 완전히 독립 package는 아니지만 **App 중심 architecture를 기능 단위 helper/class로 분리**해 한 파일 비대화를 제어한다.

# 5. Scene의 핵심은 Elements + AppState + BinaryFiles

```text
Elements
  작품 자체의 객체

AppState
  선택/도구/줌/패널/환경

BinaryFiles
  이미지 등 대용량 데이터
```

하지만 핵심은 세 종류를 나눈 것보다 **각각의 저장·history 규칙을 동일하게 취급하지 않는 것**이다.

# 6. 상태 저장은 field별 목적지 정책으로 푼다

`APP_STATE_STORAGE_CONF`:

각 AppState field마다:
- browser
- export
- server

포함 여부를 선언한다.

예:
- zoom → browser yes / export no / server no
- selectedElementIds → browser yes / export no / server no
- grid state 일부 → browser/export/server yes
- viewport width/height → 저장 no

## 문제 해결

AppState 전체를 serialize하면:
- 내 선택 상태
- 열린 dialog
- 화면 크기
- hover
가 작품 파일에 들어간다.

Excalidraw은 **field 단위로 저장 의도를 코드화**한다.

# 7. 변경 자체도 Durable / Ephemeral로 나눈다

`CaptureUpdateAction`:

### IMMEDIATELY
의미 있는 local change. 즉시 undo에 들어감.

### NEVER
remote update, initialization처럼 local undo에 들어가면 안 되는 변경.

### EVENTUALLY
drag/async 과정처럼 지금은 중간 상태지만 후에 capture될 수 있는 변경.

따라서 상태 분류와 별개로:

```text
"이번 변경은 사용자가 되돌릴 하나의 의미 있는 행동인가?"
```

를 판단한다.

# 8. History는 Delta 기반이며 collaboration 의미를 알고 있다

`HistoryDelta`는 StoreDelta를 역산해 undo/redo한다.

특히:
- version/versionNonce를 그대로 되돌리지 않음
- undo/redo도 협업 관점에서 새 user action
- AppState만 바뀌었다고 redo stack을 지우지 않음
- element 변경이 발생할 때 redo invalidation

## 의미

Undo가 단순 "과거 snapshot 복원"이면 remote collaboration과 충돌한다.

Excalidraw은 undo를 **현재 상태 위에 적용되는 새 변경**으로 다룬다.

# 9. local-first는 단일 저장소가 아니다

`LocalData`:

```text
Elements
+ filtered AppState
      ↓
localStorage

BinaryFiles
      ↓
IndexedDB
```

기능:
- debounce
- flushSave
- quota 감지
- collaboration 중 save lock
- hidden document save pause
- binary file lastRetrieved 갱신
- 오래된 unused binary 정리

## 왜 분리했나

localStorage:
- 간단한 JSON
- 용량 작음

IndexedDB:
- binary/대용량
- 비동기

데이터 특성에 맞춰 저장소를 다르게 쓴다.

# 10. BinaryFiles는 FileManager가 lifecycle을 담당

FileManager는:
- fetch 중 상태
- file version
- errored files
- upload/save
- status
등을 추적한다.

즉 image는 단순 DataURL field가 아니라 **독립 lifecycle을 가진 resource**다.

# 11. realtime과 durable collaboration을 분리

## Realtime
Portal + Socket:
- room
- scene broadcast
- presence/user
- pointer
- 빠른 전달

## Durable
Firebase:
- encrypted scene
- scene version
- binary file storage

```text
Realtime socket
≠
Durable database
```

이 분리가 중요하다.

# 12. E2EE는 room key를 data path에 넣어 해결

Firebase scene은 client에서:
- elements JSON encode
- roomKey로 encrypt
- ciphertext + IV 저장

load:
- ciphertext
- roomKey decrypt
- restore
- reconcile

room key는 URL fragment/hash 기반 처리 경로를 사용해 server request에 직접 노출되지 않도록 설계된 부분도 source에 존재한다.

## 장점
storage provider가 plaintext scene을 직접 다루지 않게 할 수 있다.

## 주의
E2EE는 key sharing/usability/recovery 문제를 함께 만든다.

# 13. 동시 수정은 reconcile + version + ordering으로 푼다

`reconcileElements()`는 local/remote element:
- id
- version
- versionNonce
- index

를 비교해 결합한다.

테스트는 양방향 reconciliation 후 동일 결과가 나오는 **convergence**를 명시적으로 검사한다.

element order는 fractional indexing 계층과 함께 안정적으로 유지하려 한다.

# 14. 렌더링과 UI chrome을 분리

React는:
- toolbar
- dialog
- menu
- panel

같은 UI chrome에 강하게 사용된다.

실제 drawing scene은 canvas renderer와 element geometry 계층이 담당한다.

즉 "모든 shape = React DOM node" 모델이 아니다.

# 15. 초보자 친화성을 코드 구조로 뒷받침하는 부분

- Action을 공통 명령으로 묶음
- phone/desktop form factor 분기
- MobileToolbar/MobileMenu 별도
- 선택/현재 상태에 따라 action predicate
- contextual panel
- sensible defaults
- open file format
- local-first로 계정 없이 시작 가능

하지만 "버튼이 적어서 쉽다" 정도로 단순화하면 안 된다.

# 16. 테스트가 보여주는 실제 위험

테스트 범위:
- history
- reconcile
- restore
- export
- selection
- arrows/binding
- images
- clipboard
- mobile menu
- viewport
- render cache
- app state
- shortcuts
- library
- collaboration
- Firebase

특히 reconcile test는:
- local/remote order
- version conflict
- concurrent insert
- duplicate index 방지
- bidirectional convergence
를 반복한다.

즉 협업의 핵심 invariant는 **모든 client가 같은 element ordering과 version resolution에 수렴하는 것**이다.

# 17. 핵심 trade-off

### 얻는 것
- 매우 쉬운 첫 사용
- open portable file
- local-first
- embed 가능한 core
- hosted realtime collaboration
- binary resource 안정성

### 지불하는 것
- AppState가 커짐
- persistence path가 여러 개
- hosted app은 socket + Firebase + IDB + localStorage 복잡성
- reconciliation 규칙이 어려움
- Action/App architecture가 역사적으로 커져 refactor 비용이 있음

# 18. 가장 재사용 가치가 큰 원리

1. **SDK와 hosted product의 책임을 분리한다.**
2. **명령은 UI/keyboard/API가 같은 action을 사용한다.**
3. **상태 field마다 저장 목적지를 선언한다.**
4. **변경을 durable/ephemeral/remote로 분류한다.**
5. **undo를 collaboration과 독립적인 과거 복원이 아니라 새 delta로 본다.**
6. **JSON state와 BinaryFiles를 분리한다.**
7. **realtime transport와 durable storage를 분리한다.**
8. **convergence를 테스트한다.**
