# 03. Twine 분석

> **2026-11-08 재검토:** 이 문서는 초기 분석 기록입니다. 현재 `new-knolstory/main`을 소스 수준으로 대조한 결과, StoryDocument v8, StoryFlow, score condition/effect/route, branch condition, Runtime Core 및 migration/validation이 이미 존재합니다. 따라서 아래의 “변수/점수를 새로 도입” 제안은 폐기합니다. 최신 판단은 [Fit–Gap 분석](systematic/02-knol-fit-gap.md)을 우선합니다.

## 한 줄 결론

Twine은 passage/link/state/story format의 분리도 중요하지만, **현재 놀스토리에 가장 가치 있는 참고 대상은 Story Map의 시각적 구조 탐색과 진단 UX**입니다. 놀스토리의 내부 실행 구조는 이미 Twine 개념을 단순 이식할 단계가 아닙니다.

## 왜 분석하는가

분기형 콘텐츠가 단순할 때는 `장면 → 선택지 → 다음 장면`만 있어도 충분합니다. 하지만 작품 수가 늘고, 과거 선택에 따라 대사가 달라지거나 특정 선택지가 나타나지 않아야 하면 구조가 급격히 복잡해집니다.

Twine은 이 복잡성을 그래프와 상태로 분리해 다룹니다.

## 확인된 사실

### A. passage가 기본 단위

Twine에서 작업한다는 것은 passage를 만드는 일입니다. passage에는 제목, 태그, 내용이 있고 다른 passage로 연결됩니다.

passage는 장면, 대화 블록, 시간대, 장소, 코드 블록 등으로 사용할 수 있습니다.

### B. link가 그래프를 만든다

`[[A passage]]` 같은 링크가 다른 passage를 가리키면 story map에서 연결선으로 나타납니다. 기본 연결은 방향성이 있는 이동입니다.

즉 저작 화면은 본질적으로 **노드 그래프 편집기**입니다.

### C. 변수는 이야기 전체 상태를 기억

Twine의 story variable은 passage를 넘어 접근할 수 있어 과거 선택, 소지품, 관계 수치, 점수, 방문 여부 등을 저장할 수 있습니다.

temporary variable은 현재 passage에서만 쓰이는 임시 계산에 적합합니다.

### D. 조건문으로 같은 노드도 다르게 보일 수 있음

story format에 따라 문법은 다르지만, 변수 값에 따라 텍스트·선택지·분기를 조건부로 표시할 수 있습니다.

이 점이 단순한 ‘분기 트리’와 ‘상태 기반 인터랙티브 이야기’의 차이입니다.

### E. story format은 실행 엔진

Twine 2에서 story format은 단순 테마가 아니라 이야기 실행 규칙과 매크로를 제공하는 엔진에 가깝습니다.

- Harlowe: 입문 친화
- SugarCube: 기능이 많고 저장 슬롯 등 제공
- Snowman: JavaScript/CSS 커스터마이징 중심
- Chapbook: 비교적 읽기 쉬운 고급 기능

저작 데이터와 실행 엔진이 분리되어 있다는 점이 중요합니다.

### F. 결과물은 HTML로 배포 가능

Twine은 최종 이야기를 HTML로 publish합니다. 편집기가 없어도 story format과 이야기 데이터가 묶인 결과물이 브라우저에서 실행됩니다.

2026-11-08 기준 공식 사이트는 Twine 2.12.1을 최신 버전으로 안내합니다.

## 핵심 설계 모델

```text
Story
 ├─ passages[]
 │   ├─ id
 │   ├─ title
 │   ├─ tags[]
 │   ├─ content
 │   └─ choices[]
 ├─ variables / state schema
 ├─ start passage
 └─ runtime format

Runtime session
 ├─ current passage
 ├─ variable values
 ├─ visit history
 ├─ inventory / flags
 └─ save / resume data
```

## 놀스토리에 가져올 구조

### 1. 장면 그래프를 데이터 모델의 1급 요소로

현재 화면에 장면을 카드로 보여주는 것과 별개로 내부 데이터는 그래프를 명확히 가져야 합니다.

권장:

```json
{
  "nodes": [
    {"id":"scene-1","type":"scene","title":"..."}
  ],
  "edges": [
    {"from":"scene-1","choiceId":"c1","to":"scene-2"}
  ]
}
```

선택지 내부에 다음 장면 전체를 중첩하는 방식보다 노드와 간선을 분리하면 재연결·시각화·오류 검사가 쉬워집니다.

### 2. 변수와 조건을 ‘고급 기능’으로 단계적으로 추가

초등학생/교사가 처음부터 코드를 쓰게 하면 안 됩니다.

Twine의 개념을 가져오되 UI는 다음처럼 시각화하는 편이 좋습니다.

- 변수 이름: `용기`
- 시작값: 0
- 선택 시: `용기 + 1`
- 조건: `용기가 2 이상이면 이 선택지 보이기`

내부에서는 expression으로 저장하더라도 화면에서는 자연어 규칙 빌더로 제공합니다.

### 3. 방문 여부를 기본 조건으로 제공

코딩 없이도 활용도가 높은 조건:

- 이 장면을 처음 방문했을 때
- 특정 장면을 이미 봤을 때
- 특정 선택을 했을 때
- 아이템을 가지고 있을 때
- 점수가 일정 이상일 때

### 4. 미리보기에서 상태를 볼 수 있게

분기형 이야기 디버깅의 가장 큰 문제는 ‘왜 이 선택지가 안 뜨는지’입니다.

미리보기 옆에 개발자 도구처럼 단순한 상태창을 둘 수 있습니다.

```text
현재 장면: 시장 입구
용기: 2
엽전: 1
방문: 우물가 ✓
선택 기록: 도와준다 → 숨는다
```

## 놀히스토리에 가져올 구조

놀히스토리는 이야기 재미뿐 아니라 역사적 맥락이 중요하므로 일반 story state 외에 **역사 상태와 학습 상태를 분리**하는 편이 좋습니다.

예:

```text
narrativeState
  - 소지품
  - 인물 관계
  - 선택 기록

learningState
  - 확인한 사료
  - 핵심 개념
  - 오개념 피드백

historicalConstraints
  - 해당 시대에 가능한 행동
  - 사건 순서상 허용되는 이동
```

이렇게 하면 재미를 위한 선택과 역사적으로 불가능한 선택을 구분할 수 있습니다.

## UX에서 가져올 패턴

### 1. 전체 이야기를 한눈에 보는 지도

노드가 많아질수록 텍스트 목록보다 그래프가 유리합니다.

필요 기능:

- 시작 노드 표시
- 도달 불가능 노드 경고
- 끝나지 않는 루프 경고
- 연결되지 않은 선택지 표시
- 조건이 있는 간선에 배지 표시

### 2. 간단한 것은 코드 없이

Twine도 단순 이야기는 코드를 몰라도 만들 수 있습니다. 놀스토리 역시 기본 분기는 지금처럼 직관적으로 유지하고, 변수/조건은 선택 기능이어야 합니다.

### 3. 저작기와 플레이어 분리

저작기 기능이 플레이어 번들에 섞이지 않게 합니다. 학생이 이야기를 읽을 때는 편집 데이터나 관리 UI를 전혀 알 필요가 없습니다.

## 그대로 가져오면 안 되는 점

### 1. Twine 문법 자체를 사용자에게 노출하지 않기

Harlowe/SugarCube 같은 텍스트 매크로는 강력하지만 초등 교육 서비스에는 과합니다.

### 2. 임의 JavaScript 실행을 허용하지 않기

사용자 작성 스크립트를 허용하면 보안과 데이터 무결성 문제가 커집니다. 조건식은 제한된 DSL이나 규칙 빌더로 관리하는 편이 낫습니다.

### 3. story format 생태계를 그대로 만들 필요는 없음

우리 서비스에는 ‘여러 런타임 엔진을 사용자가 선택’하는 것보다 하나의 안정된 플레이어와 테마/레이아웃 옵션이 적합합니다.

## 구현 실험 — 2026-11-08 재판정

### 실험 W1 — 읽기 전용 Story Map

기존 StoryDocument/StoryFlow를 변경하지 않고 다음을 시각화합니다.

- 시작/끝 컷
- choice/goto 간선
- 연결되지 않은 목적지
- 조건/점수 route 배지
- 클릭 시 기존 편집 화면으로 이동

### 실험 W2 — cycle 정책 fixture

현재 `findStoryFlowIssues()`는 DFS에서 발견되는 cycle을 모두 문제로 처리합니다. 하지만 반복 탐색이나 상태 변화 후 탈출하는 루프는 의도된 이야기 구조일 수 있습니다.

다음 케이스를 제품 정책으로 먼저 분류합니다.

- 탈출 선택이 있는 loop
- 탈출 없는 loop
- 점수 증가 후 탈출하는 loop
- self-link + 종료 선택
- 상태 변화 없는 자동 goto loop

그 뒤 error / warning / 허용으로 validator를 정교화합니다.

### 실험 W3 — 조건 디버거

현재 score/branch 조건이 이미 있으므로 새 DSL을 만들기보다 “왜 이 선택지가 보임/숨김인가”를 설명하는 미리보기 도구를 검토합니다.

## 판단

- **Story Map/구조 진단 UX:** 매우 높음
- **놀스토리 엔진 구조 이식:** 불필요 — 현재 구현이 이미 더 구체적임
- **변수·점수·조건 신규 구현:** ALREADY
- **놀히스토리:** 핵심 경험이 분기형으로 결정될 경우에만 ADAPT
- **Twine 코드 직접 재사용:** 낮음. 구조·UX 패턴 참고가 적합

## 근거 링크

- Twine: https://twinery.org/
- Passages: https://twinery.org/cookbook/introduction/passages.html
- Variables: https://twinery.org/cookbook/terms/terms_variables.html
- Story formats: https://twinery.org/cookbook/starting/twine2/storyformat.html
- Linking passages: https://twinery.org/reference/en/editing-stories/linking-passages.html
- GitHub: https://github.com/klembot/twinejs
