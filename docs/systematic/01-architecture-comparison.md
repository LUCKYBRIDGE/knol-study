# 5개 도구 아키텍처 교차 분석

검토일: 2026-11-08

## 1. 핵심 구조를 한 장으로

```text
tldraw
Input/UI
  ↓
Editor
  ↓
Reactive Store ── Schema / Migration
  ├─ document
  ├─ session
  └─ presence
  ↓
Persistence / Sync

H5P
Platform
  ↓
Content Type Manifest (library.json)
  ↓
Semantics / Authoring
  ↓
Content Instance
  ↓
Runtime Library
  ↓
Events / State / Platform integration

Twine
Authoring Story Map
  ↓
Story / Passages / Links
  ↓
Story Format
  ↓
Published Runtime
  ↓
Runtime variables/history

Artsteps
Asset collection
  ↓
3D space construction
  ↓
Artifact placement
  ↓
Guide points / narrative
  ↓
Viewer / sharing

Excalidraw
Editor UI
  ↓
Elements + AppState + BinaryFiles
  ↓
Storage-purpose filtering
  ├─ browser
  ├─ export
  └─ server
  ↓
Local persistence / files / collaboration
```

## 2. tldraw — 강점은 편집기가 아니라 상태 인프라

### 확인된 실제 패키지 경계 (A1)
공식 저장소는 최소한 다음을 별도 패키지로 둔다.
- `state`
- `store`
- `editor`
- `tlschema`
- `sync-core`
- `sync`

이 분리는 '캔버스 라이브러리'가 하나의 거대한 컴포넌트가 아니라 **상태→저장→편집→동기화 계층**으로 구성됨을 보여준다.

### record scope (A2)
- document: 지속 저장 + 사용자 간 동기화
- session: 사용자/탭별, 공유하지 않음
- presence: 저장하지 않고 사용자 간 순간 동기화

### 더 깊은 교훈
**상태의 의미에 따라 lifecycle을 정한다.**
데이터 타입을 만든 뒤 "어디 저장할까?"가 아니라, 그 데이터의 생명주기를 먼저 지정한다.

### 놀 서비스로
ADOPT:
- canonical document / session / presence 개념
- schema migration
- asset lifecycle 별도 관리

REJECT/DEFER:
- tldraw SDK 전체 도입을 기본 해법으로 삼는 것
- 요구가 없는 실시간 협업 선행 구현

## 3. Excalidraw — 상태 목적별 저장 정책이 더 세밀함

### 실제 소스 확인 (A1)
`packages/excalidraw/appState.ts`의 `APP_STATE_STORAGE_CONF`는 AppState의 각 필드를:
- browser
- export
- server
각 목적에 저장할지 개별 boolean으로 선언한다.

예:
- zoom: browser만
- selectedElementIds: browser만
- gridSize: browser/export/server
- collaborator: 어느 저장에도 포함하지 않음
- viewport width/height: 저장하지 않음

이는 tldraw의 scope와 비슷하지만 다른 축이다.

**tldraw 질문:** 이 레코드는 어떤 lifecycle인가?
**Excalidraw 질문:** 이 상태를 어느 저장 목적지에 포함할 것인가?

둘을 결합하면 더 좋은 내부 설계를 만들 수 있다.

### LocalData 실제 소스 (A1)
- elements + 일부 AppState → localStorage
- BinaryFiles → IndexedDB
- debounce 저장
- collaboration 중 local save lock 가능
- quota exceeded 감지
- 오래 사용되지 않은 binary file 정리

### 놀 서비스로
ADOPT:
```text
StateFieldPolicy
  persistBrowser?
  exportFile?
  shareServer?
  syncRealtime?
  includeInHistory?
```

이 정책 표를 저장 포맷 설계 문서에 명시하면 UI 상태가 작품 파일에 섞이는 문제를 줄일 수 있다.

## 4. H5P — 확장은 코드가 아니라 manifest + schema 계약

### library.json (A1)
실제 `H5P.MultiChoice`는:
- machineName
- semantic version 구성
- runnable 여부
- coreApi 버전
- preload JS/CSS
- runtime dependencies
- editorDependencies
를 선언한다.

즉 content type은 단순 React 컴포넌트 같은 것이 아니라 **버전과 dependency를 가진 배포 단위**다.

### semantics.json (A1)
MultiChoice semantics에서 확인되는 필드:
- type
- importance
- widget
- optional/default
- list/group
- nested library
- showWhen

InteractiveVideo는:
- wizard editor
- timeline duration
- interactions list
- x/y/width/height
- small screen에서 button 전환 옵션
등을 schema에 포함한다.

### 중요한 교훈
H5P의 schema는 단순 데이터 검증기가 아니다.
**저작 UI 메타데이터 + 데이터 계약 + 일부 반응형 편집 규칙**을 함께 가진다.

### 놀 서비스로
ADAPT:
- registry + manifest
- 데이터 validation
- editor capability metadata
- version/dependency 선언

주의:
우리 제품의 모든 UI를 generic schema form으로 만들지 않는다. 학습용 핵심 UX는 전용 편집기를 유지한다.

## 5. Twine — 엔진보다 authoring graph가 강점

### 공식 UX (A2)
Story Map:
- passage 카드
- 방향 링크
- start passage 표시
- broken link 빨간 표시
- empty passage 시각 구분
- tag 색상
- fuzzy search

### 실제 코드 구조 (A1)
Twine 저장소의 store 층은:
- stories
- persistence
- undoable-stories
- story-formats
를 별도 모듈로 둔다.

즉 authoring 상태와 undo, persistence, format extension이 분리되어 있다.

### 놀스토리와 비교
현재 KnolStory Next에는 이미:
- StoryDocument SSOT
- schemaVersion 8
- StoryFlow
- score definitions/conditions/effects/routes
- branch condition
- Runtime Core
- RuntimeScene/RuntimeCommand
- Ren'Py Web runtime
- local persistence / archive format
이 존재한다.

따라서 Twine에서 **엔진 구조를 이식할 이유가 없다.**

ADAPT:
- Story Map
- broken/incomplete link 표시
- 태그/검색
- 분기 디버거
- 조건이 왜 보이는지/숨겨지는지 설명

ALREADY:
- 변수/점수 조건
- branch conditions
- runtime 분리
- 문서 버전과 검증

## 6. Artsteps — 공개적으로 검증할 수 있는 것은 authoring→guide→share

공식 사이트에서 확인되는 흐름:
1. digital content upload
2. VR space build
3. guide point 배치
4. narrative 제공
5. share/embed

소스 코드가 공개되지 않으므로 내부 카메라 알고리즘이나 데이터 포맷은 추론하지 않는다.

### 놀아트와 비교
현재 `artwork-gallery-3d.mjs`는:
- THREE.js 기반
- 1/3인칭 전환
- walk/run/jump
- character model
- x/z 이동
- 작품 인접 여부 판정
- 좌우 작품 이동 `stepWork()`
를 이미 제공한다.

하지만 카메라는 기본적으로 자신의 앞(-z)을 바라보고, 작품별 `eye/lookAt` 관람점이나 순서 route abstraction은 현재 파일에서 확인되지 않는다.

따라서 실제 gap은:
- 3D 기술 없음 ❌
- 이동 없음 ❌
- 캐릭터 없음 ❌
- **감상 동선 abstraction 없음 ✅**
- **작품별 viewpoint 없음 ✅**
- **guided tour 상태 없음 ✅**

## 7. 다섯 도구를 통합해 얻는 자체 아키텍처 원칙

```text
Canonical Content
  ↓
Schema + Migration + Validation       ← tldraw / H5P
  ↓
Authoring Application
  ├─ task-focused UI                   ← Excalidraw / H5P
  ├─ graph/map diagnostics             ← Twine
  └─ history
  ↓
Storage Policy
  ├─ browser                           ← Excalidraw
  ├─ export
  ├─ server
  └─ realtime
  ↓
Runtime / Viewer
  ├─ story runtime                     ← Twine
  └─ guided viewing                    ← Artsteps
```

이것을 하나의 새 프레임워크로 만들지 않는다. 이미 각 서비스에 존재하는 경계를 보존하면서 필요한 부분만 강화한다.
