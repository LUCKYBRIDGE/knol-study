# 서비스별 마스터 분석

검토 기준: 2026-11-08

이 디렉터리는 분석한 앱·웹을 **서비스 하나 단위로 완결성 있게 찾기 위한 마스터 인덱스**다.

기존 문서의 역할:
- `structure/` — 전체 구조와 데이터 흐름
- `forensics/` — 왜 그런 구조로 문제를 풀었는가
- `systematic/` — Knol에 무엇을 채택할 것인가
- `cases/` — 신규 제품 1차 심층 분석

`services/`는 위 내용을 제품별로 다시 묶고, 다음을 빠뜨리지 않는다.

1. 실제 서비스/저장소 폴더·파일 구조
2. 주요 페이지·route·surface
3. 화면별 UI/UX
4. 주요 버튼·메뉴·도구
5. 기능과 내부 메커니즘
6. 사용 기술
7. 데이터·저장·동기화 흐름
8. 복잡하게 얽힌 문제를 푼 방식
9. 오류·복구·권한·테스트
10. 장점·trade-off
11. source evidence와 Unknown

## 현재 정리 대상

- [tldraw](tldraw/README.md)
- [Excalidraw](excalidraw/README.md)
- [H5P](h5p/README.md)
- [Twine](twine/README.md)
- [Artsteps / Metasteps](artsteps-metasteps/README.md)
- [Penpot](penpot/README.md)
- [AFFiNE](affine/README.md)
- [Microsoft MakeCode / PXT](makecode/README.md)
- [Padlet](padlet/README.md)

## 문서 규칙

각 제품 폴더:
```text
README.md
01-repository-files.md
02-pages-ui-ux-controls.md
03-technology-data-flow.md
04-problem-solving-patterns.md
```

공개 source가 없으면 `01-repository-files.md`에 "비공개"를 명시하고 내부 구조를 지어내지 않는다.
