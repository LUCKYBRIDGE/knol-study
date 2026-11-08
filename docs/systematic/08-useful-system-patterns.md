# 유용한 구조·설계·시스템 패턴 카탈로그

검토 기준: 2026-11-08

이 문서는 특정 제품을 따라 만들기 위한 것이 아니라, 여러 앱·웹·교육 도구에서 반복해서 쓸 수 있는 **설계 원리와 시스템 패턴**을 정리한다.

---

# A. 상태와 데이터

## A-01. Canonical Document

### 구조

```text
Canonical Document
      ↓
Validation
      ↓
Editor
      ↓
Runtime / Export / Share
```

콘텐츠의 진실 원천을 하나로 둔다.

### 좋은 점
- 여러 화면에서 데이터 불일치가 줄어든다.
- import/export가 쉬워진다.
- migration이 가능해진다.
- 테스트 fixture를 만들기 쉽다.

### 위험
- Document에 UI 상태까지 넣으면 다시 복잡해진다.

### 참고
tldraw, KnolStory

---

## A-02. Document / Session / Presence 분리

### 구조

```text
Document
  실제 콘텐츠

Session
  현재 사용자 화면 상태

Presence
  협업 중 순간 상태
```

### 좋은 점
- 공유 파일에 개인 줌/선택 상태가 들어가지 않는다.
- 협업 기능을 나중에 붙이기 쉽다.
- autosave 범위를 결정하기 쉽다.

### 위험
- 아주 작은 앱에서 지나치게 많은 store를 만들 수 있다.

### 참고
tldraw

---

## A-03. 저장 목적지별 State Policy

### 구조

각 필드에 저장 대상을 선언한다.

```text
Field
 ├─ browser?
 ├─ export?
 ├─ server?
 ├─ realtime?
 └─ history?
```

### 좋은 점
- export 파일이 깔끔하다.
- 개인정보·UI 상태가 서버로 불필요하게 가지 않는다.
- persistence 버그를 줄일 수 있다.

### 위험
- 정책표와 실제 구현이 달라질 수 있으므로 테스트가 필요하다.

### 참고
Excalidraw

---

# B. 저장·복구·동기화

## B-01. Metadata와 Binary Asset 분리

### 구조

```text
Document JSON
 └─ assetId references

Asset Store
 ├─ image blob
 ├─ audio blob
 └─ file metadata
```

### 좋은 점
- 대용량 JSON을 피할 수 있다.
- asset별 garbage collection 가능.
- IndexedDB/서버 업로드를 최적화하기 쉽다.

### 위험
- document는 있는데 asset이 없는 orphan 문제가 생길 수 있다.
- export 패키지에는 자산 포함 규칙이 필요하다.

### 참고
tldraw, Excalidraw

---

## B-02. Debounced Autosave

사용자 입력마다 즉시 큰 저장을 하지 않고 짧게 모아 저장한다.

### 좋은 점
- 저장 I/O 감소
- UI 끊김 감소

### 위험
- 탭을 닫기 직전 변경이 아직 flush되지 않을 수 있다.
- 저장 성공 표시와 실제 저장 완료를 구분해야 한다.

### 필수 보완
- flushSave
- visibility/page lifecycle 처리
- 마지막 정상 저장본

---

## B-03. Recoverable Failure

저장 오류가 발생해도 기존 정상본을 지우지 않는다.

### 원칙
- quota 실패를 명시
- 손상된 recovery를 자동 삭제하지 않음
- export 탈출구 제공
- 이전 정상본 유지

### 참고
놀아트 현재 구현, Excalidraw

---

## B-04. Opt-in Recovery

모든 앱이 full autosave를 필요로 하지는 않는다.

### 적합한 경우
- 파일 변환 도구
- 민감한 원본을 다루는 도구
- 일회성 작업이 많은 서비스

### 구조

```text
기본: 메모리만 사용

사용자 선택:
"이 기기에 임시 보관"
     ↓
IndexedDB
     ↓
TTL / 최근 N개 / 전체 삭제
```

### 좋은 점
- 작업 복구와 privacy 사이 균형

### 위험
- 사용자가 저장 범위를 오해할 수 있으므로 UI 문구가 중요

---

## B-05. Schema Version + Migration

저장 포맷은 반드시 버전을 가진다.

```text
v1 → migrate → v2 → migrate → v3
```

### 좋은 점
- 오래된 작품 보존
- 기능 추가 시 파괴적 변경 방지

### 위험
- 버전 숫자만 있고 migration test가 없으면 의미가 없다.

---

# C. 편집기 UX

## C-01. Always / Contextual / Advanced

기능을 노출 수준으로 분류한다.

### Always
지금 작업의 핵심 행동.

### Contextual
현재 선택/단계와 관련된 기능.

### Advanced
빈도가 낮거나 이해 비용이 큰 기능.

### 좋은 점
- 초기 화면의 인지 부담 감소
- 핵심 작업 집중

### 위험
- 과도하게 숨기면 발견성이 떨어짐
- 버튼 개수 자체가 목표가 되면 안 됨

### 검증
과제 완료율, 잘못 클릭, 도움말 요청으로 평가.

### 참고
Excalidraw

---

## C-02. Task-oriented Editor

기술 객체 구조보다 사용자의 작업 단위로 편집 UI를 만든다.

예:
- "배경 설정"
- "등장인물 배치"
- "선택지 만들기"

가 내부 데이터의 복잡한 필드보다 먼저 보인다.

### 좋은 점
초보자가 데이터 구조를 몰라도 된다.

### 위험
고급 사용자에게는 너무 제한적으로 느껴질 수 있다.

---

## C-03. Visual Structural Diagnostics

오류를 목록뿐 아니라 실제 구조 위에 표시한다.

예:
- 끊어진 간선 빨간색
- 비어 있는 노드
- 미연결 선택지 배지
- 조건 edge 아이콘

### 좋은 점
'무슨 오류인가'뿐 아니라 '어디서 생겼는가'를 즉시 이해.

### 참고
Twine

---

# D. 확장 가능한 콘텐츠 시스템

## D-01. Versioned Type Manifest

### 구조

```text
ActivityType
 ├─ id
 ├─ version
 ├─ schema
 ├─ runtime dependencies
 ├─ editor dependencies
 ├─ author
 └─ player
```

### 좋은 점
새 활동이 늘어날수록 core 수정이 줄어든다.

### 위험
활동이 1~2개일 때부터 만들면 premature abstraction.

### 참고
H5P

---

## D-02. Schema-driven Authoring Metadata

데이터 schema가 편집기 힌트도 일부 가진다.

예:
- importance
- widget
- optional
- default
- showWhen

### 좋은 점
공통 editor infrastructure를 만들 수 있다.

### 위험
모든 UX를 generic form으로 만들면 사용성이 떨어진다.

### 적절한 사용
- 설정 패널
- 고급 옵션
- 반복적이고 비핵심적인 편집 UI

---

## D-03. Author / Runtime Separation

```text
Authoring
  만들기 / 수정 / 검증

Runtime
  읽기 / 풀기 / 관람
```

### 좋은 점
학생용 화면이 단순해진다.
편집 코드가 런타임 번들에 덜 섞인다.

### 참고
H5P, Twine, KnolStory

---

# E. 그래프·분기 시스템

## E-01. Graph as First-class Structure

분기 콘텐츠는 tree보다 graph가 자연스럽다.

```text
Node
Edge
Condition
State
```

### 좋은 점
- 분기 합류
- 재방문
- 조건부 연결
- 구조 시각화
- validator

를 처리하기 좋다.

### 위험
초보자 UI에서 graph 개념을 그대로 노출하면 어렵다.

### 참고
Twine

---

## E-02. Static Graph와 Runtime State 분리

```text
Story Graph
  고정 구조

Runtime State
  현재 위치
  선택 기록
  변수/점수
```

### 좋은 점
같은 작품을 다른 플레이 상태로 실행 가능.

---

## E-03. Cycle을 Product Policy로 다루기

cycle 자체는 반드시 오류가 아니다.

구분 예:
- 탐색을 위한 loop
- 탈출 선택이 있는 loop
- 상태 변화 후 탈출 loop
- 탈출 없는 강제 loop
- 상태 변화 없는 자동 loop

### 좋은 점
validator가 실제 제품 의도와 맞아진다.

---

# F. 3D·관람 시스템

## F-01. Artifact Transform과 Viewpoint 분리

```text
Artwork
 └─ transform

Viewpoint
 ├─ eye
 └─ lookAt
```

작품 위치와 '사람이 어디서 봐야 하는가'는 다른 정보다.

### 좋은 점
- 자동 카메라 정렬
- 다음/이전 작품
- 모바일 guided viewing
- 작품별 적정 거리

### 참고
Artsteps 개념, 놀아트 적용 후보

---

## F-02. Free Roam + Guided Route 병행

### Free Roam
직접 돌아다니는 경험.

### Guided
이전/다음 또는 순서 관람.

### 좋은 점
사용자 숙련도에 따라 다른 경로 제공.

### 위험
모드가 많으면 혼란.

### 권장
기본은 쉬운 guided, 자유 이동은 선택 기능으로 검토.

---

## F-03. 3D Fallback

3D는 반드시 대체 경로를 둔다.

예:
- 2D 작품 목록
- 작품 카드
- 바로 이동
- reduced motion

### 이유
접근성, 멀미, 저사양 기기, 조작 실패.

---

# G. 검증·안전·운영

## G-01. Validate Before Replace

새 파일을 불러올 때 먼저 완전히 검증한 뒤 현재 작업 상태를 교체한다.

### 좋은 점
잘못된 파일 하나 때문에 현재 정상 작업을 잃지 않는다.

### 참고
놀아트 현재 구현

---

## G-02. Resource Budget

복잡한 작업 파일에는 명시적인 한도가 필요하다.

예:
- 최대 이미지 용량
- 최대 객체 수
- 최대 undo
- 최대 point
- 최대 history bytes

### 좋은 점
브라우저 탭 폭주와 메모리 문제를 예방.

---

## G-03. Explicit Dependency Boundary

외부 라이브러리/SDK는 다음을 구분한다.

- 아이디어 참고
- 코드 일부 사용
- 전체 SDK 도입
- hosted service 의존

### 좋은 점
라이선스·벤더 종속성을 줄인다.

---

# H. 설계 시 빠르게 묻는 12개 질문

새로운 Knol 앱이나 기능을 설계할 때 다음 순서로 확인한다.

1. 결과물의 canonical data는 무엇인가?
2. 화면 상태와 결과물 데이터가 분리되어 있는가?
3. 어떤 상태가 browser/export/server/realtime에 필요한가?
4. 이미지·음원 같은 binary는 어떻게 관리하는가?
5. 저장 실패 시 사용자는 작업을 잃는가?
6. schemaVersion과 migration이 필요한가?
7. 저작 화면과 실행 화면은 분리되어야 하는가?
8. 기능은 항상 보여야 하는가, 선택했을 때만 보여야 하는가?
9. 관계가 복잡하다면 목록보다 graph가 필요한가?
10. 3D라면 자유 이동 외에 guided path가 있는가?
11. 새 콘텐츠 타입을 추가할 때 core를 얼마나 수정해야 하는가?
12. 현재 실제 문제보다 추상화·프레임워크를 먼저 만들고 있지는 않은가?

이 12개 질문은 새로운 앱·웹·도구를 분석할 때도 동일하게 사용할 수 있다.
