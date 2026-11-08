# 91. 구현 실험 백로그 — v2

> 2026-11-08 현재 Knol 저장소 main과 외부 소스를 다시 대조해 갱신했습니다. 초기 제안 중 이미 구현된 항목은 제거하거나 ALREADY로 전환했습니다.

## P0 — 실제 현재 gap

### K-001 놀아트 작품별 viewpoint + guided route

**근거:** Artsteps의 guide point 개념 + 현재 `artwork-gallery-3d.mjs` 대조.

현재 이미 존재:
- THREE.js 3D 전시
- 1/3인칭
- walk/run/jump
- 작품 간 `stepWork()`
- 작품 인접 상호작용

새 실험:
- `ExhibitViewpoint { artworkId, eye, lookAt }`
- 이전/다음 작품
- 작품별 감상 거리
- reduced-motion 즉시 이동
- 관람 순서 상태

완료 조건:
- 자유 이동을 쓰지 않아도 모든 작품 접근
- 벽 관통 없음
- 태블릿에서도 이전/다음만으로 감상 가능
- 2D 작품 목록 fallback 유지

### K-002 놀스토리 읽기 전용 Story Map

**근거:** Twine Story Map.

현재 이미 존재:
- StoryDocument v8
- choice/goto
- score condition/effect/route
- branch condition
- flow validator
- Runtime Core

새 실험:
- node/edge 시각화
- start/end
- incomplete/broken link 강조
- 조건 edge badge
- node 클릭 → 기존 컷 편집기로 이동

1단계에서는 graph에서 직접 편집하지 않습니다.

### K-003 놀스토리 cycle 정책 결정

현재 validator는 cycle을 모두 문제로 표시합니다. 기술 오류인지 제품 정책인지 분리해야 합니다.

fixture:
1. 탈출 선택이 있는 loop
2. 탈출 없는 loop
3. 상태 변화 후 탈출 loop
4. self-link + 종료 선택
5. 상태 변화 없는 자동 goto loop

각 케이스를 valid / warning / error로 먼저 합의한 뒤 validator를 수정합니다.

### K-004 저장 대상 정책표

**근거:** tldraw lifecycle scope + Excalidraw `APP_STATE_STORAGE_CONF`.

놀아트/놀프린트의 주요 state를 다음 목적별로 분류:
- browser recovery
- export file
- share/server
- realtime/presence
- history

완료 조건:
- UI transient state가 작품 파일에 우연히 들어가지 않음
- binary asset가 누락되지 않음
- 저장/공유 목적별 테스트 fixture 존재

## P1 — UX 검증

### K-101 놀프린트 opt-in recovery prototype

현재 원본은 메모리에서만 처리되고 설정만 localStorage에 저장됩니다. 이 privacy/단순성 특성을 유지합니다.

실험:
- 기본 OFF
- 사용자가 “이 기기에 임시 보관” 선택 시에만 Blob을 IndexedDB 저장
- TTL/최근 N개/전체 삭제
- quota 실패 안내
- 원본과 설정 분리

**full autosave를 기본값으로 만들지는 않습니다.**

### K-102 놀아트·놀프린트 progressive disclosure A/B

모든 기능을 무조건 숨기는 것이 아니라:
- Always
- Contextual
- Advanced
로 분류한 시안을 현재 UI와 과제 기반 비교.

측정:
- 완료율
- 잘못 클릭
- 도움말 필요
- 작업 시간
- 결과 오류

### K-103 놀스토리 조건 디버거

기존 score/branch condition을 새로 만드는 대신 플레이 테스트에서:
- 현재 점수
- 선택 기록
- 조건 평가 결과
- 숨겨진 선택지의 사유
를 개발/저작 모드에서 설명.

## P2 — 확장 구조

### K-201 H5P식 Activity Manifest spike

**우선 대상:** 놀히스토리보다 기존 놀퀴즈의 실제 활동 3종.

검토 계약:
- id/version
- data schema
- capabilities
- author component
- player component
- migration
- dependencies

통과 조건:
네 번째 타입 추가 때 core 저장/라우팅/import/export 수정이 최소.

### K-202 놀히스토리 thin vertical prototype

activity registry나 branching engine을 먼저 구현하지 않습니다.

먼저 결정:
- 1순위 사용자
- 한 개 역사 사건/학습 단위
- 핵심 user job
- 중심 인터페이스
- 사실/해석/창작 경계
- source metadata

한 단위를 처음부터 끝까지 만든 뒤 반복되는 구조만 추출합니다.

## P3 — 선행조건 후

### K-301 실시간 협업
DEFER. document/session/presence와 conflict 정책이 실제 요구될 때.

### K-302 공통 persistence 패키지
DEFER. 놀아트·놀프린트·다른 편집기에서 동일 API가 반복되는 것이 확인된 뒤 추출.

### K-303 고급 3D 캐릭터
DEFER. 현재 캐릭터/걷기/달리기/점프가 이미 존재합니다. 관람 viewpoint와 카메라 안정성 이후 평가.

## ALREADY — 새로 만들지 않을 것

- 놀아트 IndexedDB recovery
- 놀아트 project schema validation
- 놀아트 undo/history budget
- 놀스토리 score state
- 놀스토리 조건/효과/conditional route
- 놀스토리 branch condition
- 놀스토리 StoryDocument version/migration
- 놀스토리 author/runtime separation

## 실험 기록 규칙

각 실험은 다음을 기록합니다.

```text
문제
현재 구현 근거
가설
최소 변경
테스트 fixture
측정 결과
회귀 위험
유지/수정/폐기 결정
다음 단계
```
