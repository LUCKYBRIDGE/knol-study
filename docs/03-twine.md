# 03. Twine 분석

## 한 줄 결론

Twine의 핵심은 ‘분기형 이야기 UI’가 아니라 **이야기를 passage 노드 + link 간선 + state 변수 + story format 런타임으로 분리하는 모델**입니다. 놀스토리와 놀히스토리에는 이 구조가 직접적인 참고 대상입니다.

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

## 구현 실험

### 실험 W1 — graph validator

저장 시 자동 검사:

- 시작점 없음
- 목적지 없는 선택지
- 도달 불가 장면
- 출구 없는 장면
- 자기 자신으로만 반복되는 장면

### 실험 W2 — flag/score 2종만 먼저

범용 변수를 처음부터 만들지 말고:

- Boolean flag
- Number score

두 종류로 시작합니다.

### 실험 W3 — 조건 빌더

```text
[조건 추가]
  무엇이?  용기 점수
  어떻게?  이상
  값       2
```

내부 표현식은 별도로 저장합니다.

## 판단

- **원리 채택:** 매우 높음
- **놀스토리 적용:** 매우 높음
- **놀히스토리 적용:** 매우 높음
- **Twine 코드 직접 재사용:** 낮음. Twine 자체는 GPL-3.0이며 우리 서비스에는 구조 참고가 더 적합

## 근거 링크

- Twine: https://twinery.org/
- Passages: https://twinery.org/cookbook/introduction/passages.html
- Variables: https://twinery.org/cookbook/terms/terms_variables.html
- Story formats: https://twinery.org/cookbook/starting/twine2/storyformat.html
- Linking passages: https://twinery.org/reference/en/editing-stories/linking-passages.html
- GitHub: https://github.com/klembot/twinejs
