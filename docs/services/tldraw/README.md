# tldraw 마스터 분석

분류: 무한 캔버스 / SDK / 협업 편집기  
근거 수준: A1/A2 매우 높음

## 한 줄 구조

```text
tldraw.com product
  → UI
  → Editor
  → typed Store
  → reactive State + Schema/Migration
  → Local Persistence / Asset Store / Sync
```

## 이 폴더에서 볼 것

- [저장소·파일 구조](01-repository-files.md)
- [페이지·UI·UX·버튼](02-pages-ui-ux-controls.md)
- [기술·데이터·저장·동기화](03-technology-data-flow.md)
- [복잡한 문제를 푼 방식](04-problem-solving-patterns.md)

## 기존 심층 문서

- [전체 구조](../../structure/01-tldraw-full-structure.md)
- [문제 해결 방식](../../forensics/01-tldraw-problem-solution.md)

## 핵심 참고 포인트

- document/session/presence 상태 수명주기
- StateNode 기반 gesture state machine
- Shape Record + ShapeUtil
- Editor facade + specialized managers
- schema/migration
- asset metadata/blob 분리
- local persistence와 multiplayer sync 분리
- server-authoritative room
- fuzz + upgrade/downgrade test
