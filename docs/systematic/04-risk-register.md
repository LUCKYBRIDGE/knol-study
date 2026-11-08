# 분석 기반 리스크 레지스터

| ID | 리스크 | 대상 | 가능성 | 영향 | 대응 |
|---|---|---|---|---|---|
| R01 | source-available SDK를 자유 오픈소스로 오해 | tldraw | 중 | 높음 | 직접 도입 전 라이선스 검토 |
| R02 | hosted app 기능을 SDK 기본 기능으로 오해 | Excalidraw | 높음 | 중 | package/app 기능 범위 분리 |
| R03 | 모든 상태를 하나의 JSON에 저장 | 공통 | 중 | 높음 | lifecycle + destination policy |
| R04 | binary asset 누락으로 복구 파일 불완전 | art/print | 중 | 높음 | asset registry/blob store |
| R05 | full autosave가 개인정보/용량 문제 유발 | print | 중 | 중~높음 | opt-in recovery, TTL |
| R06 | generic schema UI가 교사 UX를 악화 | H5P 응용 | 중 | 중 | 핵심 타입 전용 UI |
| R07 | content type registry를 너무 일찍 추상화 | history/quiz | 중 | 높음 | 3개 실제 타입 후 추출 |
| R08 | Twine 구조를 놀스토리에 중복 구현 | story | 중 | 높음 | 현재 domain/runtime 우선 |
| R09 | 정상적인 cycle을 오류로 제한 | story | 현재 정책 | 중 | 제품 정책 재결정 |
| R10 | 3D 캐릭터 개선에 집중해 관람성 정체 | art | 높음 | 중 | viewpoint/tour 우선 |
| R11 | guided camera가 멀미/벽 관통 | art | 중 | 중 | reduced-motion + safe waypoint |
| R12 | 3D만 제공해 접근성 저하 | art | 중 | 높음 | 2D 작품 목록 fallback |
| R13 | H5P 전체가 MIT라고 오해 | H5P | 중 | 높음 | 구성요소별 license 확인 |
| R14 | 분기 점수를 학생 성취 점수와 혼동 | story/history | 중 | 높음 | plot state와 assessment 분리 |
| R15 | 새 공통 패키지가 서비스 독립성을 해침 | 공통 | 중 | 중 | 검증 후 추출, premature sharing 금지 |
| R16 | schema migration 없이 저장 포맷 변경 | 공통 | 낮~중 | 매우 높음 | 버전+fixture migration tests |
| R17 | multi-tab 동시 수정 데이터 손실 | edit tools | 중 | 높음 | 충돌/동기화 정책 명시 |
| R18 | 저장 성공 UI와 실제 flush 불일치 | 공통 | 중 | 높음 | 저장 상태 machine/flush 테스트 |

## 가장 중요한 리스크 5개

### 1. 새 기술 도입보다 기존 구조 훼손
놀스토리는 이미 강한 domain/runtime 경계를 갖는다. 외부 도구 분석 결과를 이유로 재설계하지 않는다.

### 2. 저장 기능 확대의 개인정보 비용
놀프린트가 원본을 저장하지 않는 것은 단점만이 아니라 privacy 특성이다. recovery는 opt-in이 더 적합하다.

### 3. 3D의 성능보다 관람 UX
놀아트에서 GLB/animation을 추가할수록 GPU 비용이 커진다. guided route가 먼저다.

### 4. 추상화 시점
H5P의 성숙한 registry는 수십 개 타입의 결과다. 초기 1~2개 기능에서 동일 구조를 만들면 과설계가 된다.

### 5. 테스트 없는 "초보자 친화"
버튼을 숨기면 쉬워지는 것이 아니다. 핵심 기능 발견률을 과제 테스트로 확인해야 한다.
