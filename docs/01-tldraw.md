# 01. tldraw 분석

## 한 줄 결론

tldraw에서 가장 가져올 만한 것은 캔버스 자체보다 **문서 상태·세션 상태·협업 상태를 분리한 편집기 아키텍처**와, 그 위에 자동 저장·실시간 동기화·undo/redo를 얹는 방식입니다.

## 왜 분석하는가

놀아트, 놀프린트처럼 사용자가 화면에서 직접 편집하는 서비스는 기능이 늘어날수록 저장·되돌리기·선택 상태·공유 상태가 뒤엉키기 쉽습니다. tldraw은 이 문제를 편집기 중심의 상태 모델로 푼 사례입니다.

## 확인된 사실

### A. Store가 편집기의 중심

tldraw의 store는 shape, page, binding, asset 등의 레코드를 보관하는 반응형 데이터베이스입니다. 변경 사항은 UI 갱신뿐 아니라 undo/redo, persistence, synchronization에도 사용됩니다.

핵심 포인트는 ‘캔버스 화면’을 저장하는 것이 아니라 **JSON 직렬화 가능한 레코드 집합**을 저장한다는 점입니다.

### B. document와 session을 분리

snapshot은 크게 두 부분으로 나뉩니다.

- `document`: 도형, 페이지, 연결, 자산 등 공유해야 할 결과물
- `session`: 카메라, 현재 페이지, 선택 상태, UI 상태 등 사용자별 작업 상태

공유 문서에서는 document를 서버에 두고 session은 사용자별 로컬에 두는 설계가 가능합니다.

### C. 자동 저장의 진입 장벽이 낮음

`persistenceKey`만 지정하면 IndexedDB에 문서와 자산을 저장하고 새로고침 후 복원하며, 같은 키를 쓰는 탭 사이에서도 동기화합니다.

더 복잡한 서비스는 store 변경 이벤트를 구독해 throttled auto-save를 구현하거나 snapshot을 별도 백엔드에 저장할 수 있습니다.

### D. 협업은 같은 store 모델 위에 올라감

`@tldraw/sync`는 실시간 협업을 같은 데이터 모델 위에서 처리합니다. 공식 multiplayer starter kit은 Durable Objects, WebSocket, SQLite, R2를 조합한 예를 제공합니다.

즉 단일 사용자 편집기와 협업 편집기가 완전히 다른 앱이 아니라, **동일 문서 모델 + 다른 persistence/sync adapter**에 가깝습니다.

### E. 최근 UX 방향도 ‘정교한 기본기’

2026-10-28 공개된 SDK 5.6에서는 사용할 수 없는 메뉴를 숨기거나 비활성화하는 조건부 메뉴와 더 정밀한 undo가 강조됐습니다. 기능 추가보다 ‘사용자가 현재 할 수 있는 행동만 보이게 하는 것’과 ‘되돌리기의 예측 가능성’을 개선한 점이 중요합니다.

## 핵심 설계 모델

```text
Editor
 ├─ Document state
 │   ├─ shapes
 │   ├─ pages
 │   ├─ bindings
 │   └─ assets
 ├─ Session state
 │   ├─ camera
 │   ├─ selection
 │   ├─ active page
 │   └─ UI state
 ├─ History
 │   └─ undo / redo
 └─ Persistence / Sync adapter
     ├─ IndexedDB
     ├─ custom backend
     └─ realtime sync
```

이 구조를 그대로 복제할 필요는 없지만, 놀퀴즈 계열 편집 도구에도 **결과물 / 현재 작업 화면 / 히스토리 / 저장 방식**을 분리하는 것이 좋습니다.

## UX에서 가져올 패턴

### 1. 저장 버튼에 의존하지 않기

사용자는 ‘저장’을 작업으로 인식하지 않아도 됩니다. 변경은 즉시 로컬에 반영하고, 상단에는 저장 상태만 조용히 표시하는 편이 적합합니다.

권장 상태:

- 저장됨
- 저장 중…
- 오프라인 — 기기에 저장됨
- 동기화 필요
- 복구 가능한 임시본 있음

### 2. 메뉴의 조건부 노출

현재 선택 대상에 적용할 수 없는 메뉴는 가능하면 숨기고, 학습을 위해 필요한 경우에만 비활성 상태로 남깁니다.

### 3. undo의 의미를 좁게 유지

‘사용자가 방금 한 조작 하나’를 되돌리는 것이 원칙입니다. 선택 변경, 패널 열기 같은 UI 이벤트가 편집 히스토리에 섞이면 안 됩니다.

### 4. 자산과 문서를 함께 생각

이미지 편집 도구에서 JSON만 저장해도 이미지 파일이 유실되면 복구가 불가능합니다. 놀아트·놀프린트도 document와 asset lifecycle을 함께 설계해야 합니다.

## 우리 서비스 적용안

### 놀아트

우선순위 높음.

- 작품 데이터와 카메라/줌/선택 상태 분리
- IndexedDB 기반 즉시 자동 저장
- 작품 schema version 도입
- undo/redo를 편집 명령 기준으로 일관되게 관리
- 2D 편집기와 3D 전시실이 같은 작품 asset id를 참조하도록 통일

### 놀프린트

우선순위 높음.

- 업로드 이미지 원본과 분할/자르기 설정을 별도 레코드화
- 브라우저 종료 후 ‘마지막 작업 계속하기’
- 인쇄 설정은 session이 아니라 document에 포함할지 명확히 결정
- undo 대상: 자르기, 배치, 분할 수, 맞물림 변경
- undo 비대상: 현재 열려 있는 패널, 확대율 같은 UI 상태

### 공통 편집기 기반

향후 여러 서비스가 편집 기능을 공유한다면 `editor-core` 성격의 내부 모듈을 만들 수 있습니다.

예:

```text
DocumentStore
HistoryManager
AssetStore
AutosaveAdapter
SchemaMigration
SelectionState
```

tldraw SDK를 직접 넣는 것보다 이 개념을 자체 공통 모듈로 먼저 정리하는 편이 현재 서비스들과 더 잘 맞습니다.

## 그대로 가져오면 안 되는 점

### 1. tldraw SDK 직접 도입은 라이선스 확인 필수

2026년 현재 tldraw SDK 기본 라이선스는 개발 환경 사용과 프로덕션 사용 조건이 다르며, 프로덕션 사용에는 별도 라이선스 키가 필요합니다. 따라서 ‘오픈 GitHub 저장소이므로 자유롭게 제품에 넣을 수 있다’고 보면 안 됩니다.

### 2. 무한 캔버스가 모든 서비스에 필요한 것은 아님

놀프린트는 A4 출력이라는 강한 물리적 제약이 있습니다. 무한 캔버스 경험을 그대로 가져오면 오히려 최종 출력 크기 감각이 약해질 수 있습니다.

### 3. 협업을 너무 빨리 넣지 않기

실시간 공동 편집은 presence, 충돌, asset 동기화, 권한, 재접속까지 설계해야 합니다. 먼저 local-first + 복구 + 공유 스냅샷이 안정화된 뒤 확장하는 편이 안전합니다.

## 구현 실험

### 실험 T1 — 공통 자동 저장 계층

- IndexedDB document store
- 300~800ms 정도의 변경 묶음 저장
- schemaVersion 포함
- 마지막 정상 저장본 + 작업 중 draft 분리
- 강제 새로고침 후 복구 테스트

### 실험 T2 — document/session 분리

놀아트 한 기능에서 먼저 적용합니다.

- 작품 요소 변경 → document
- 줌/스크롤/선택 → session
- 새 기기에서 문서를 열었을 때 session 없이도 정상 표시되는지 검증

### 실험 T3 — undo 이벤트 정리

현재 편집 명령을 목록화한 뒤 ‘데이터 변경 명령’만 history에 들어가도록 정리합니다.

## 판단

- **원리 채택:** 매우 높음
- **구조 참고:** 매우 높음
- **SDK 직접 도입:** 라이선스·기존 코드 구조를 따로 검토한 뒤 판단

## 근거 링크

- Persistence: https://tldraw.dev/docs/persistence
- Store: https://tldraw.dev/sdk-features/store
- Multiplayer starter kit: https://tldraw.dev/starter-kits/multiplayer
- SDK 5.6: https://tldraw.dev/blog/tldraw-sdk-5-6
- GitHub: https://github.com/tldraw/tldraw
- License: https://github.com/tldraw/tldraw/blob/main/LICENSE.md
