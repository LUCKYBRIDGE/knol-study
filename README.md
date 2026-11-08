# knol-study

놀퀴즈 계열 서비스와 신규 교육 도구를 개선하기 위해 외부 앱·웹·도구를 분석하고, 재사용 가능한 설계 원리로 축적하는 저장소입니다.

> 분석 기준일: 2026-11-08  
> 분석 방식: `craft-anything`의 Study → Evidence → Check 관점을 참고하되, 단순 복제보다 **관찰 가능한 UX / 공개 문서·코드 근거 / 설계 해석 / 우리 서비스 적용 / 검증 과제**를 분리합니다.

## 1차 분석 대상

| 순서 | 대상 | 핵심 관찰 주제 | 우선 적용 후보 |
|---|---|---|---|
| 1 | [tldraw](docs/01-tldraw.md) | 편집기 UX, 상태 모델, 자동 저장, 공유·동기화 | 놀아트, 놀프린트, 공통 편집기 기반 |
| 2 | [H5P](docs/02-h5p.md) | 교육 활동 유형, 모듈형 콘텐츠, 재사용·이식 | 놀퀴즈 확장, 신규 교육활동 도구 |
| 3 | [Twine](docs/03-twine.md) | 분기 그래프, 변수, 조건, 런타임 분리 | 놀스토리, 놀히스토리 |
| 4 | [Artsteps](docs/04-artsteps.md) | 3D 전시실, 관람 동선, 가이드 포인트 | 놀아트 3D 전시실 |
| 5 | [Excalidraw](docs/05-excalidraw.md) | 초보자 친화 UI, 로컬 우선, 공개 포맷 | 놀아트, 놀프린트 |

## 공통 결론

다섯 도구에서 공통으로 가져올 만한 방향은 다음과 같습니다.

1. **문서 데이터와 사용자 세션 상태를 분리한다.** 저장·공유·복구·협업이 쉬워집니다.
2. **콘텐츠를 하나의 거대한 페이지가 아니라 재사용 가능한 타입/노드로 본다.** H5P와 Twine이 특히 강합니다.
3. **제작 도구(authoring)와 실행/관람(runtime)을 분리한다.** 학생용 화면을 단순하게 유지하기 좋습니다.
4. **자동 저장은 기능이 아니라 신뢰 UX다.** 저장 여부·복구 가능성·마지막 저장 상태가 사용자에게 명확해야 합니다.
5. **초보자 화면은 적게 보여주고, 선택한 대상에 필요한 기능만 노출한다.** Excalidraw 방식이 참고 가치가 큽니다.
6. **3D 공간에서는 완전 자유 이동만 제공하지 말고 안내 동선과 순간 이동을 함께 제공한다.** Artsteps의 guide point 개념이 유용합니다.

상세 비교와 구현 우선순위는 [교차 분석](docs/90-cross-analysis.md)에 정리합니다.

## 분석 원칙

- 공개 문서와 공개 코드에서 확인되는 사실과, 그 사실을 바탕으로 한 설계 추론을 구분합니다.
- 특정 제품의 UI를 그대로 복제하지 않고 패턴과 원리를 추출합니다.
- 라이선스가 허용되는 경우에도 코드 직접 차용보다 구조·인터랙션 원리 참고를 우선합니다.
- 학교 현장에서는 학생 계정, 개인정보, 네트워크 불안정, 태블릿 조작성을 별도 제약으로 봅니다.

## 디렉터리

```text
docs/
  00-methodology.md
  01-tldraw.md
  02-h5p.md
  03-twine.md
  04-artsteps.md
  05-excalidraw.md
  90-cross-analysis.md
  91-implementation-backlog.md
```


## 심층 분석 (2026-11-08 보강)

각 도구의 **실제 공개 문서 기반 구조**, 데이터 계약, 실패 모드, 기기별 테스트 기준을 더 상세히 정리했습니다.

- [tldraw 기술·UX 심층 분석](docs/deep/01-tldraw-technical.md)
- [H5P 콘텐츠 엔진 심층 분석](docs/deep/02-h5p-technical.md)
- [Twine 분기 엔진 심층 분석](docs/deep/03-twine-technical.md)
- [Artsteps 3D 관람 UX 심층 분석](docs/deep/04-artsteps-ux.md)
- [Excalidraw 초보자 편집기 심층 분석](docs/deep/05-excalidraw-ux.md)
- [검증 기록과 확인되지 않은 항목](docs/92-evidence-audit.md)

**주의:** `craft-anything` CLI로 대상 서비스를 직접 실행·계측한 결과는 아닙니다. 다만 2026-11-08 재검토에서는 tldraw·H5P·Twine·Excalidraw의 공식 소스 구조와 **현재 Knol 저장소 main 코드까지 직접 대조**했습니다. 실제 브라우저 행동 계측·네트워크 trace는 아직 별도 증거 단계입니다.

## 체계적 분석 v2 — 먼저 읽을 문서

1차/심층 문서보다 더 엄격한 공통 기준과 현재 코드 대조를 사용합니다. **내용이 충돌할 경우 아래 v2 문서를 우선합니다.**

- [분석 프레임워크 v2](docs/systematic/00-analysis-framework-v2.md) — A1~D 증거 등급 + 10개 공통 분석 축
- [5개 도구 아키텍처 비교](docs/systematic/01-architecture-comparison.md)
- [현재 Knol 저장소 Fit–Gap](docs/systematic/02-knol-fit-gap.md)
- [재사용 설계 패턴 카탈로그](docs/systematic/03-pattern-catalog.md)
- [리스크 레지스터](docs/systematic/04-risk-register.md)
- [적용 실험 프로토콜](docs/systematic/05-experiment-protocols.md)
- [현재 의사결정표](docs/systematic/06-decision-matrix.md)

### 재분석 후 바뀐 핵심 판단

- **놀스토리:** Twine의 변수·점수·조건·런타임 구조를 새로 도입하지 않습니다. 현재 main에 이미 더 정교한 StoryDocument/Flow/score/condition/Runtime Core가 존재합니다. Twine에서는 Story Map과 시각적 진단 UX를 가져옵니다.
- **놀아트:** IndexedDB recovery와 project validation은 이미 존재합니다. 새 저장 엔진보다 상태 저장 정책 정리와 3D 작품별 viewpoint/guided route가 우선입니다.
- **놀프린트:** 모든 원본을 자동 영속화하는 것은 현재의 개인정보·단순성 특성을 훼손할 수 있습니다. 필요하면 opt-in 임시 복구를 실험합니다.
- **놀히스토리:** H5P식 activity registry나 Twine식 branching engine을 지금 구현하지 않습니다. 먼저 첫 핵심 학습 경험과 thin vertical prototype을 확정합니다.


## 설계 레퍼런스

분석 결과를 구현 우선순위와 별개로 **구조·설계·시스템·장단점 관점에서 다시 찾아볼 수 있게** 정리했습니다.

- [설계 레퍼런스 시작점](docs/REFERENCE.md)
- [도구별 구조·시스템·장단점](docs/systematic/07-tool-structures-pros-cons.md)
- [재사용 가능한 구조·설계·시스템 패턴](docs/systematic/08-useful-system-patterns.md)

새 서비스를 설계할 때는 먼저 패턴 카탈로그를 보고, 실제 Knol 프로젝트 적용 여부는 Fit–Gap과 의사결정표에서 별도로 판단합니다.


## 앱·웹 전체 구조도

각 도구를 단순 기능 소개가 아니라 **제품 경계, 공개 코드 모듈, 데이터 모델, 저장·실행·공유 흐름까지** 구조도로 정리했습니다.

- [전체 구조도 인덱스](docs/structure/README.md)
- [5개 도구 구조 비교 지도](docs/structure/06-cross-structure-map.md)

공개 소스가 없는 Artsteps는 내부 구현을 추측으로 확정하지 않고, 공식적으로 확인 가능한 제품 구조와 논리적 추론 구조를 구분했습니다.


## 제품 포렌식 분석 — 구조가 문제를 푸는 방식

구조도보다 한 단계 더 깊게, 각 제품이 **어떤 문제를 왜 이런 구조로 풀었는지** 분석했습니다.

- [포렌식 분석 시작점](docs/forensics/README.md)
- [같은 문제를 서로 다르게 푼 방식](docs/forensics/06-same-problem-different-solution.md)
- [문제 → 설계 메커니즘 카탈로그](docs/forensics/07-problem-mechanism-catalog.md)
- [테스트·품질 전략 비교](docs/forensics/08-quality-testing-strategy.md)
- [기능 → 내부 메커니즘 지도](docs/forensics/09-feature-capability-mechanism-map.md)

`docs/structure/`는 **무엇이 어디에 있는지**, `docs/forensics/`는 **왜 그렇게 풀었는지**를 담당합니다.
