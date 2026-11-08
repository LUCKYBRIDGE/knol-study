# Excalidraw 마스터 분석

분류: 무한 캔버스 / 로컬 우선 / 협업 화이트보드  
근거 수준: A1 매우 높음

## 한 줄 구조

```text
Elements + AppState + BinaryFiles
 → Action / StoreDelta / History
 → localStorage + IndexedDB
 → hosted collaboration: Socket + Firebase
```

- [저장소 구조](01-repository-files.md)
- [페이지·UI·UX·버튼](02-pages-ui-ux-controls.md)
- [기술·데이터 흐름](03-technology-data-flow.md)
- [문제 해결 방식](04-problem-solving-patterns.md)

기존:
- [전체 구조](../../structure/02-excalidraw-full-structure.md)
- [포렌식 분석](../../forensics/02-excalidraw-problem-solution.md)
