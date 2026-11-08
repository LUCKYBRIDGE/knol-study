# 구조도 레퍼런스 — 읽는 법

검토 기준: 2026-11-08

이 디렉터리는 각 앱·웹을 가능한 한 **제품 전체 구조** 관점에서 정리한다.

## 구조도의 5개 층

각 도구는 다음 순서로 본다.

1. **Product Boundary** — 무엇이 core이고 무엇이 hosted app/service인가
2. **Repository / Module Topology** — 공개 소스의 디렉터리·패키지 경계
3. **Domain & State Model** — 데이터가 어떤 종류로 나뉘는가
4. **Runtime / Persistence Flow** — 입력부터 저장·실행까지 어떻게 흐르는가
5. **Extension & Operations** — 플러그인·동기화·업그레이드·배포 지점

## 완성도 표기

| 표시 | 의미 |
|---|---|
| **A1** | 공식 공개 소스에서 직접 확인 |
| **A2** | 공식 기술문서/규격으로 확인 |
| **B** | 공식 서비스·제품 설명으로 확인 |
| **C** | 위 근거를 조합한 구조적 추론 |
| **Unknown** | 외부에서 확인할 수 없음 |

'완벽한 구조도'라는 표현은 **공개적으로 확인 가능한 범위에서 최대한 완전하게 정리한다**는 뜻으로 사용한다. 비공개 서버·DB·내부 운영 인프라는 임의로 채워 넣지 않는다.

## 문서

- [tldraw 전체 구조](01-tldraw-full-structure.md)
- [Excalidraw 전체 구조](02-excalidraw-full-structure.md)
- [H5P 전체 구조](03-h5p-full-structure.md)
- [Twine 전체 구조](04-twine-full-structure.md)
- [Artsteps 관찰 가능 전체 구조](05-artsteps-observable-structure.md)
- [5개 도구 구조 비교](06-cross-structure-map.md)

## 구조도를 볼 때 중요한 질문

- 어떤 데이터가 제품의 진실 원천인가?
- 편집 상태와 결과물 상태가 분리되어 있는가?
- binary asset은 어디에 존재하는가?
- 저장과 실시간 동기화는 같은 시스템인가?
- 제작기와 실행기는 같은 코드인가?
- 확장 기능은 core 수정 없이 추가 가능한가?
- 버전이 바뀌었을 때 오래된 결과물을 어떻게 살리는가?
- hosted service 기능과 embeddable library 기능의 경계는 어디인가?
