# tldraw — 기술·UX 심층 검토

검토일: 2026-11-08. **검증 수준: 공식 문서 기반 코드·기능 확인(A), 실제 사용자 조작 테스트는 미실시.**

## 1. 작업 흐름 해부
- 초기화: Tldraw 컴포넌트 → store 생성/연결 → snapshot 복원 → editor 구성.
- 편집: UI 명령 → 레코드 변경 → 반응형 파생 상태 및 렌더 갱신 → history 및 persistence/sync 소비.
- 공유: 문서 데이터를 동기화하고 개인 카메라·선택·뷰는 따로 유지한다.
- 복구: 로컬 `persistenceKey`를 통한 IndexedDB 문서/업로드 자산 자동 보존과 탭 간 동기화가 공식 문서에 확인된다.
- 주의: 서버에 JSON 스냅샷만 저장하고 blob을 빠뜨리면 이미지가 깨질 수 있다.

## 2. 확인된 구조
| 구분 | 내용 | 근거 |
|---|---|---|
| document | shapes/pages/bindings/assets; 저장 및 공유 | [Store](https://tldraw.dev/sdk-features/store) |
| session | 현재 page, camera, selection; 사용자/탭별 | [Persistence](https://tldraw.dev/sdk-features/persistence) |
| presence | 커서 등 실시간 상대방 상태; 영속화 대상 아님 | [Store](https://tldraw.dev/sdk-features/store) |
| history | record 변경 추적 기반 undo/redo | [Store](https://tldraw.dev/sdk-features/store) |
| remote sync | TLSocketRoom, sync-core 저장 어댑터 | [Sync](https://tldraw.dev/docs/sync) |
| schema | 레코드 유효성 검사, 버전 migration | [Store](https://tldraw.dev/sdk-features/store) |

### store.listen 필터를 활용한 분리
공식 API는 변경 `source`(user/remote/all), `scope`(document/session/presence/all)를 구별한다. 이를 참고하여 우리 편집기의 자동 저장 이벤트는 반드시 **document 변경에만** 반응하도록 설계한다. 선택/줌 이벤트가 대용량 이미지를 반복 저장하게 만들면 안 된다.

### 자동 저장 설계 제안 — 실제 tldraw 코드의 복제 아님
```ts
type PersistedDocument = { format: "knolart"; schemaVersion: number; revision: number; objects: unknown[]; assetIds: string[] };
type EditorSession = { zoom: number; selectedIds: string[]; activePanel: string | null };
type SaveStatus = "clean" | "dirty" | "saving" | "saved" | "error";
```
상태 전이: document change → dirty → debounce → IndexedDB transaction → saved. 실패 시 error, 메모리상의 dirty는 보존. 브라우저 종료 이벤트에만 의존하지 않는다. 큰 asset는 별도 object store. `revision`을 사용해 저장 경쟁의 역전(write-after-write) 방지.

## 3. 중요한 설계상의 경계
1. **history ≠ persistence**. Undo 기록은 최신 저장본과 달리 재생/되돌리기 가능한 변경 이력이다. 모든 action을 영속화할 필요는 없다.
2. **session ≠ presence**. 내가 보고 있는 화면 상태와 상대 사용자에게 보여주는 순간적 상태를 혼동하지 않는다.
3. **asset blob ≠ asset metadata**. 이미지 id/크기/위치 데이터만으로 원본 복원이 보장되지 않는다.
4. **로컬 자동 저장 ≠ 서버 공유**. IndexedDB만으로는 기기 사이 작품이 공유되지 않는다.
5. **동시 편집 ≠ 같은 JSON 덮어쓰기**. 협업에는 충돌 처리 및 동기화 프로토콜이 필요하다.

## 4. 놀아트/놀프린트에 적용
**놀아트**: 작품 객체/document, 확대/선택/session, 작품 방문자 카메라/viewer session 분리. 하나의 asset registry를 2D 편집기·3D 전시가 참조. 작품 ID와 리비전을 내보내기 파일에도 포함.

**놀프린트**: 원본 파일 및 변환(크롭, 회전), 분할 설정을 문서 데이터로; 패널 열림, 확대율은 session으로. 출력 미리보기는 document를 가공한 *파생 결과*로 계산.

### 거부해야 할 도입
- 화면 구조가 다르다는 이유로 tldraw SDK 전체를 도입하지 않는다.
- A4 물리 출력은 무한 캔버스보다 절대 크기/페이지 경계가 중요하다.
- cloud sync를 넣기 전 복구·기기 내 안정성을 우선 검증한다.

## 5. 테스트 매트릭스
| 테스트 | 절차 | 통과 기준 |
|---|---|---|
| 강제 새로고침 | 이미지 업로드·자르기 후 reload | 동일 작품 및 원본 이미지 복원 |
| 저장 충돌 | 연속 빠른 30회 편집 | 마지막 revision만 최종 저장 |
| 멀티탭 | 같은 작품을 두 탭에서 수정 | 정책대로 동기화 또는 충돌 경고; 조용한 데이터 손실 없음 |
| 스키마 변경 | 이전 버전 JSON 업로드 | migration 성공 또는 명확한 오류 |
| 용량 부족 | 대용량 이미지 반복 | 실패 알림 + 기존 정상본 보존 |
| history 의미 | 도형 수정→패널 열기→undo | 도형 수정만 되돌림 |
| 신규 장치 | document만 전달 | session 없는 상태에서 동일한 작품 렌더 |

## 6. 라이선스·신뢰성
[tldraw 라이선스](https://tldraw.dev/community/license)는 SDK를 source-available로 분류하며 프로덕션 실행에 trial/commercial/hobby 라이선스 키가 필요하다. hobby 라이선스는 재량 발급·워터마크 조건이 있다. **코드 직접 도입을 즉시 권장하지 않는다.**

**남은 검증**: 실제 tldraw 웹 편집기에서 툴바·복구·공유를 PC/태블릿별 수동 시나리오로 관찰하고 캡처 저장. 기존 저장소에 대한 정량 성능 측정은 수행하지 않았다.
