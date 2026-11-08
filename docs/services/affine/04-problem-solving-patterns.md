# AFFiNE — 얽힌 문제 해결

| 문제 | 해결 |
|---|---|
| 문서와 화이트보드가 별도 silo | shared block/workspace model |
| offline과 cloud 중 하나 선택 | local-first + optional cloud |
| concurrent edit | CRDT |
| binary가 CRDT document를 비대화 | blob 분리 |
| cloud가 local ownership을 빼앗음 | cloud를 sync/backup/service layer로 |
| 단순 viewer/editor 권한 부족 | capability permission |
| 여러 client | shared core + desktop/mobile/web |
| page가 많아 탐색 복잡 | collections/tags/journals/all pages |
| editor 기능 증가 | BlockSuite extension/widget |

## Knol에 유용
- local-first canonical + optional cloud
- document/blob 분리
- 같은 콘텐츠의 다른 view
- capability permission

## 당장 복제 금지
모든 Knol 서비스에 CRDT를 선행 도입할 필요는 없다.
