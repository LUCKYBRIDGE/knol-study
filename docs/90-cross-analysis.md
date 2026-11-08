# 90. 교차 분석

## 핵심 결론

다섯 도구를 함께 보면 서로 경쟁 관계라기보다 서로 다른 문제를 잘 푼 사례에 가깝습니다.

| 도구 | 가장 잘 참고할 문제 | 핵심 패턴 | 우리 적용 우선 대상 |
|---|---|---|---|
| tldraw | 복잡한 편집기 상태 | document / session / history / sync 분리 | 놀아트, 놀프린트 |
| H5P | 교육활동 확장성 | content type registry + schema + package | 놀퀴즈, 신규 교육도구 |
| Twine | 분기 복잡도 | node / edge / state / runtime 분리 | 놀스토리, 놀히스토리 |
| Artsteps | 3D 관람 동선 | free roam + guided route + artifact | 놀아트 전시실 |
| Excalidraw | 초보자 UX | progressive disclosure + local-first | 놀아트, 놀프린트 |

## 1. 가장 중요한 공통 원리: 데이터와 화면 상태를 분리

tldraw에서 가장 선명하지만 모든 서비스에 적용됩니다.

```text
콘텐츠 데이터
  ≠
현재 사용자의 화면 상태
  ≠
실행 중 세션 상태
```

예:

놀아트
- 작품 객체: 콘텐츠 데이터
- 현재 선택/줌: 편집 세션
- 3D 관람객 위치: 관람 세션

놀스토리
- 노드/선택지: 콘텐츠 데이터
- 편집기에서 선택한 노드: 편집 세션
- 플레이 중 현재 장면/변수: 실행 세션

이 구분이 되면 자동 저장, 공유, 복구, 미리보기, 협업이 서로 덜 충돌합니다.

## 2. 두 번째 원리: ‘기능’이 아니라 ‘타입’을 추가

H5P가 가장 잘 보여줍니다.

나쁜 확장:

```text
새 활동 요청
→ 새 HTML 페이지
→ 새 저장 코드
→ 새 내보내기 코드
→ 새 UI 규칙
```

권장 확장:

```text
새 활동 타입
→ registry 등록
→ schema 정의
→ author renderer
→ player renderer
→ 공통 저장/내보내기 사용
```

이 구조는 신규 서비스가 많아지는 현재 상황에서 특히 중요합니다.

## 3. 세 번째 원리: 제작기와 실행기를 분리

Twine, H5P, Artsteps 모두 형태는 다르지만 같은 방향을 보여줍니다.

```text
Authoring
  - 만들기
  - 수정
  - 구조 확인
  - validation

Runtime
  - 읽기/풀기/관람
  - 최소 UI
  - 세션 상태
```

학생이 사용하는 실행 화면은 교사가 사용하는 제작 화면보다 훨씬 단순해야 합니다.

## 4. 네 번째 원리: 저장은 ‘기능’보다 ‘신뢰 시스템’

tldraw과 Excalidraw의 local-first 접근에서 가져올 점:

- 작업 즉시 로컬 저장
- 명시적 저장 버튼에 의존하지 않음
- 새로고침 복구
- schema version
- export/import 탈출구
- 네트워크가 없어도 최소 기능 유지

놀퀴즈 계열에서 공통 AutosaveAdapter를 만드는 것이 여러 서비스에 반복적으로 도움이 될 가능성이 큽니다.

## 5. 다섯 번째 원리: 초보자에게 전체 기능을 보여주지 않기

Excalidraw의 접근을 공통 UI 규칙으로 만들 수 있습니다.

### Always

현재 작업의 핵심 행동. 5~9개 정도.

### Contextual

선택한 객체나 현재 단계에서만 필요한 행동.

### Advanced

자주 쓰지 않거나 이해 비용이 높은 기능.

새 기능을 추가할 때 ‘버튼을 어디 넣을까?’보다 먼저 **어느 노출 계층에 속하는가?**를 결정합니다.

## 6. 3D에서는 자유보다 길찾기가 먼저

Artsteps 분석에서 가장 중요한 결론입니다.

3D 전시 품질을 다음 순서로 개선하는 편이 합리적입니다.

1. 작품 감상 위치(viewpoint)
2. 다음/이전 작품
3. 자동 카메라 정렬
4. 모바일 이동
5. 미니맵
6. 캐릭터 이동 애니메이션
7. 고급 환경/효과

캐릭터 모델을 먼저 바꾸면 첫인상은 좋아질 수 있지만 관람 동선 문제는 남습니다.

## 7. 분기형 콘텐츠는 tree가 아니라 graph + state로

놀스토리/놀히스토리에는 Twine의 관점이 중요합니다.

단순 tree:

```text
A
├─ B
│  └─ D
└─ C
   └─ E
```

실제 이야기는:

- 여러 선택이 같은 장면으로 합쳐짐
- 이전 선택에 따라 같은 장면이 달라짐
- 특정 조건에서만 선택지가 보임
- 다시 이전 장소로 돌아감

때문에 graph + state가 적합합니다.

## 8. 콘텐츠 재사용의 단위는 작아질수록 가치가 커짐

H5P에서 얻을 수 있는 중요한 교훈:

```text
전체 작품 복제
  ↓
장면 복제
  ↓
활동 블록 복제
  ↓
문항/요소 재사용
```

예를 들어 놀평어와는 성격이 다르지만, 놀퀴즈/놀스토리 계열에서는 ‘내 자료함’이 전체 프로젝트뿐 아니라 작은 블록도 보관할 수 있으면 재사용성이 커집니다.

## 직접 도입 vs 원리 참고

| 대상 | 원리 참고 | 코드/SDK 직접 도입 | 비고 |
|---|---:|---:|---|
| tldraw | 매우 높음 | 신중 | 프로덕션 라이선스 조건 확인 필수 |
| H5P | 매우 높음 | 낮음~중간 | 플랫폼 전체보다 content-type 개념이 더 유용 |
| Twine | 매우 높음 | 낮음 | GPL-3.0, 우리 UX에는 자체 그래프 모델이 적합 |
| Artsteps | 높음 | 낮음 | 서비스 UX 사례로 참고 |
| Excalidraw | 매우 높음 | 중간 | MIT, 특정 컴포넌트 도입 여부는 별도 검토 |

## 공통 내부 모듈 후보

여러 프로젝트에서 반복해서 사용할 수 있는 내부 패키지 후보:

```text
@knol/editor-core
  - history
  - command
  - selection

@knol/persistence
  - IndexedDB
  - autosave
  - recovery
  - migrations

@knol/content-schema
  - versioned schema
  - validation
  - import/export

@knol/activity-core
  - activity registry
  - author/player contract

@knol/story-core
  - graph
  - conditions
  - state
  - validator

@knol/tour-core
  - exhibit viewpoint
  - route
  - visited state
```

처음부터 모노레포 공용 패키지로 크게 만들기보다, 한 서비스에서 검증한 뒤 추출하는 방식을 권장합니다.

## 우선순위

### P0 — 지금 체감이 큰 것

- 놀아트 3D guided tour
- 놀아트/놀프린트 UI를 Always / Contextual / Advanced로 재분류
- 공통 auto-save / recovery 패턴

### P1 — 구조 부채를 줄이는 것

- 놀스토리 graph validator
- story flag / score condition
- 콘텐츠 schemaVersion / migration 규칙

### P2 — 확장 기반

- activity registry
- import/export package
- reusable block library
- 실시간 협업

## 최종 판단

이 다섯 도구를 그대로 섞어 하나의 거대한 프레임워크를 만들 필요는 없습니다.

가장 좋은 조합은:

```text
tldraw의 상태 모델
+ H5P의 콘텐츠 타입 구조
+ Twine의 그래프/상태 모델
+ Artsteps의 관람 동선
+ Excalidraw의 노출 절제
```

를 각 서비스의 문제에 맞게 선택적으로 적용하는 것입니다.
