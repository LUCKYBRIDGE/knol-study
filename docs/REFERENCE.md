# Knol Study — 설계 레퍼런스

이 페이지는 분석 결과를 **나중에 다시 꺼내 쓰기 위한 입구**다.

## 빠르게 볼 때

### 도구 자체를 이해하고 싶다면
- [tldraw / H5P / Twine / Artsteps / Excalidraw 구조·시스템·장단점](systematic/07-tool-structures-pros-cons.md)

### 특정 설계 문제의 해법을 찾고 싶다면
- [유용한 구조·설계·시스템 패턴 카탈로그](systematic/08-useful-system-patterns.md)

### 현재 Knol 프로젝트에 실제로 필요한지 확인하려면
- [현재 Knol 저장소 Fit–Gap](systematic/02-knol-fit-gap.md)
- [현재 의사결정표](systematic/06-decision-matrix.md)

### 왜 그런 판단을 했는지 확인하려면
- [5개 도구 아키텍처 비교](systematic/01-architecture-comparison.md)
- [분석 프레임워크 v2](systematic/00-analysis-framework-v2.md)
- [검증 기록](92-evidence-audit.md)

## 문서 역할 구분

```text
외부 도구 이해
  ↓
07-tool-structures-pros-cons.md

설계 패턴 재사용
  ↓
08-useful-system-patterns.md

우리 서비스와 비교
  ↓
02-knol-fit-gap.md

채택 여부 결정
  ↓
06-decision-matrix.md

실제 실험
  ↓
05-experiment-protocols.md
```

## 원칙

이 저장소의 목적은 특정 서비스를 복제하는 것이 아니다.

**좋은 구조를 분해하고 → 장단점을 이해하고 → 필요한 부분만 현재 서비스에 맞게 재구성하는 것**이 목적이다.


## 전체 구조도

앱·웹의 내부 관계를 최대한 전체적으로 보고 싶다면:

- [구조도 레퍼런스](structure/README.md)
- [tldraw 전체 구조](structure/01-tldraw-full-structure.md)
- [Excalidraw 전체 구조](structure/02-excalidraw-full-structure.md)
- [H5P 전체 구조](structure/03-h5p-full-structure.md)
- [Twine 전체 구조](structure/04-twine-full-structure.md)
- [Artsteps 관찰 가능 구조](structure/05-artsteps-observable-structure.md)
- [5개 도구 전체 구조 비교](structure/06-cross-structure-map.md)

구조도는 **제품 경계 → 저장소/모듈 → 데이터·상태 → 런타임 → 저장·공유·확장**의 5개 층으로 정리되어 있습니다.


### 구조를 더 깊게 검증할 때
- [소스 근거 지도](structure/07-source-evidence-map.md)
- [데이터·상태 생명주기 비교](structure/08-data-lifecycle-matrix.md)
- [쓰기·읽기·저장·실패 흐름](structure/09-read-write-persistence-failure-flows.md)
- [완성도·Unknown 관리](structure/10-completeness-and-unknowns.md)
- [완전 구조 분석 템플릿](structure/11-full-architecture-analysis-template.md)


## 제품 포렌식 — 설계 의도와 문제 해결 방식

- [포렌식 분석 인덱스](forensics/README.md)
- [tldraw 문제 해결 방식](forensics/01-tldraw-problem-solution.md)
- [Excalidraw 문제 해결 방식](forensics/02-excalidraw-problem-solution.md)
- [H5P 문제 해결 방식](forensics/03-h5p-problem-solution.md)
- [Twine 문제 해결 방식](forensics/04-twine-problem-solution.md)
- [Artsteps/Metasteps 문제 해결 방식](forensics/05-artsteps-problem-solution.md)
- [동일 문제의 서로 다른 해법](forensics/06-same-problem-different-solution.md)
- [문제 → 메커니즘 카탈로그](forensics/07-problem-mechanism-catalog.md)
- [품질·테스트 전략](forensics/08-quality-testing-strategy.md)
- [기능 → 메커니즘 지도](forensics/09-feature-capability-mechanism-map.md)


## 추가 제품 Case Studies

- [Cases 인덱스](cases/README.md)
- [Penpot](cases/penpot/README.md)
- [AFFiNE](cases/affine/README.md)
- [MakeCode / PXT](cases/makecode/README.md)
- [Padlet](cases/padlet/README.md)
- [다음 분석 로드맵](roadmap/01-next-analysis-candidates.md)
