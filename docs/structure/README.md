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


## 심층 보조 문서

- [소스 근거 지도](07-source-evidence-map.md)
- [데이터·상태 생명주기 비교](08-data-lifecycle-matrix.md)
- [쓰기·읽기·저장·실패 흐름](09-read-write-persistence-failure-flows.md)
- [분석 완성도와 미확인 영역](10-completeness-and-unknowns.md)
- [새 도구용 완전 구조 분석 템플릿](11-full-architecture-analysis-template.md)

앞으로 새 도구는 가능하면 이 템플릿의 30개 항목으로 분석한다.


## 포렌식 분석과의 관계

이 디렉터리는 **구조·데이터 흐름·모듈 경계**를 설명합니다.  
제품이 왜 그런 구조를 택했는지와 실제 문제 해결 방식은 [../forensics/README.md](../forensics/README.md)에서 이어서 분석합니다.
