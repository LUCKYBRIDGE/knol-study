# tldraw — 얽힌 문제를 풀어낸 방식

| 얽힌 문제 | 해결 방식 | 대가 |
|---|---|---|
| drag/resize/edit 상태가 꼬임 | 계층형 StateNode | 상태 클래스 증가 |
| shape 종류 증가 | Shape Record + ShapeUtil | polymorphism 이해 필요 |
| Editor가 비대해짐 | Manager 분해 | 내부 객체 수 증가 |
| 개인 UI 상태가 문서에 섞임 | document/session/presence | lifecycle 설계 비용 |
| 구버전 파일 | migrations | 지속적 migration 유지 |
| binary가 문서 JSON을 비대화 | TLAssetStore 분리 | orphan/GC 고려 |
| reload 작업 손실 | IndexedDB local persistence | quota/IDB failure |
| 여러 사용자 충돌 | authoritative room + rebase | server 운영 필요 |
| stale client | schema/protocol compatibility | update UX 필요 |
| 예측 못한 동시성 회귀 | sync fuzz test | test infra 비용 |

## Knol에서 우선 가져올 것

- 복잡 gesture → state machine
- canonical record + schema
- session/document 분리
- asset/blob 분리
- migration fixture
- 저장과 sync를 같은 기능으로 착각하지 않기

## 그대로 복제하지 않을 것

작은 Knol 도구마다 tldraw 전체 store/sync stack을 넣는 것은 과도하다.
