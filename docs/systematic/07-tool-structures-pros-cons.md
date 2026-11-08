# 도구별 구조·설계·시스템·장단점 레퍼런스

검토 기준: 2026-11-08

이 문서는 tldraw, H5P, Twine, Artsteps, Excalidraw를 **기능 목록이 아니라 제품을 지탱하는 구조와 시스템** 관점에서 정리한다.  
구현 우선순위를 정하는 문서가 아니라, 이후 새로운 서비스·편집기·학습도구를 설계할 때 다시 꺼내 보는 참고 자료다.

---

# 1. tldraw

## 한 줄 요약

**복잡한 편집기를 안정적으로 만들기 위한 상태·저장·동기화 계층 분리의 좋은 사례.**

## 핵심 구조

```text
Input / UI
    ↓
Editor
    ↓
Reactive State
    ↓
Store
    ├─ document records
    ├─ session records
    └─ presence records
    ↓
Schema / Migration
    ↓
Persistence / Sync
```

공식 저장소도 `state`, `store`, `editor`, `tlschema`, `sync-core`, `sync`처럼 관심사를 패키지 수준으로 분리한다.

## 중요한 시스템

### 1. Record scope

#### document
작품 자체의 데이터.

예:
- 도형
- 텍스트
- 페이지
- 자산 참조

특성:
- 저장 대상
- 공유 대상
- 여러 사용자 간 동기화 가능

#### session
현재 사용자의 편집 상태.

예:
- 현재 선택
- 카메라
- 줌
- 현재 도구
- 열린 패널

특성:
- 작품 그 자체는 아님
- 사용자·탭별 상태

#### presence
다른 사용자에게 순간적으로 보여 주는 협업 상태.

예:
- 마우스 위치
- 현재 보고 있는 위치
- 사용자 상태

특성:
- 실시간 전송 가능
- 장기 저장은 보통 불필요

## 설계적으로 유용한 점

- 작품 데이터와 편집 상태가 섞이는 것을 막기 쉽다.
- 실시간 협업을 나중에 추가하기 좋다.
- 상태를 어떤 생명주기로 다뤄야 하는지 명확하다.
- 저장 포맷 변경 시 schema migration을 적용하기 좋다.
- 편집기 UI와 데이터 저장 시스템을 독립적으로 발전시킬 수 있다.

## 장점

- 복잡한 편집기에서 상태 관리가 체계적이다.
- 장기적으로 기능이 늘어도 구조가 무너지기 어렵다.
- offline/local persistence와 server sync를 같은 데이터 모델 위에서 다루기 좋다.
- undo/history, asset, collaboration을 별도 계층으로 발전시키기 쉽다.

## 단점·주의

- 작은 도구에는 구조가 과할 수 있다.
- Store/Schema/Sync 개념을 모두 도입하면 초기 개발비가 커진다.
- tldraw SDK 자체 도입은 라이선스 조건을 별도로 검토해야 한다.
- '상태 분리' 원리는 유용하지만 반드시 tldraw의 구현을 그대로 사용할 필요는 없다.

## 참고 가치가 큰 서비스

- 놀아트
- 복잡한 웹 편집기
- 향후 협업 가능한 도구
- 여러 문서/작품을 다루는 제작 서비스

---

# 2. H5P

## 한 줄 요약

**교육 활동을 페이지가 아니라 버전·의존성·저작기·실행기를 가진 '콘텐츠 타입'으로 관리하는 사례.**

## 핵심 구조

```text
Platform
   ↓
Content Type
   ├─ library.json
   ├─ semantics.json
   ├─ runtime code
   ├─ editor dependencies
   └─ runtime dependencies
   ↓
Content Instance
   ↓
Player / Runtime
   ↓
Events / State / LMS integration
```

## 중요한 시스템

### 1. library.json

콘텐츠 타입 자체의 메타데이터를 정의한다.

예:
- machineName
- major/minor/patch version
- runnable
- core API version
- JavaScript/CSS
- runtime dependency
- editor dependency

즉 '객관식'은 단순 UI 컴포넌트가 아니라 **버전이 있는 독립 콘텐츠 모듈**이다.

### 2. semantics.json

단순 validation schema보다 넓은 역할을 한다.

표현 가능한 것:
- field type
- group / list
- default
- optional
- importance
- widget
- nested library
- showWhen

따라서 데이터 구조와 저작 UI 메타데이터가 연결된다.

### 3. Authoring / Runtime 분리

같은 콘텐츠라도:
- 만드는 화면
- 학생이 실행하는 화면
이 다르다.

이 분리는 교육 서비스에서 특히 중요하다.

## 설계적으로 유용한 점

새로운 활동을 만들 때:

```text
새 HTML 페이지
→ 별도 저장 코드
→ 별도 내보내기
```

가 아니라,

```text
새 Activity Type
→ manifest
→ schema
→ author
→ player
```

방식으로 확장할 수 있다.

## 장점

- 교육활동 종류가 늘어날수록 유지보수성이 좋아진다.
- 콘텐츠 타입별 버전 관리가 가능하다.
- 공통 저장/import/export를 만들기 쉽다.
- 활동을 다른 환경에 재사용할 가능성이 높아진다.
- 고급 설정을 schema의 importance 등으로 단계화할 수 있다.

## 단점·주의

- 타입이 몇 개 없는 초기 서비스에는 과설계가 될 수 있다.
- 모든 UI를 schema 기반 generic form으로 만들면 UX가 평범하고 복잡해질 수 있다.
- activity dependency가 많아지면 호환성 관리가 어려워진다.
- H5P 전체 구성요소가 하나의 동일 라이선스라고 가정하면 안 된다.
- content type registry를 너무 빨리 만들면 실제 사용자 경험보다 프레임워크 구축에 시간을 쓰게 된다.

## 참고 가치가 큰 서비스

- 놀퀴즈 활동 확장
- 반복되는 교육 콘텐츠 유형이 많은 서비스
- 콘텐츠 재사용·복사·공유가 중요한 시스템
- 장기적으로 plugin/activity 생태계를 만들 서비스

---

# 3. Twine

## 한 줄 요약

**분기 이야기를 전체 문장 목록이 아니라 노드·링크·상태·실행기로 바라보게 하는 저작 도구.**

## 핵심 구조

```text
Story
 ├─ Passage
 ├─ Link
 ├─ Tag
 └─ Start
      ↓
Story Map
      ↓
Story Format
      ↓
Runtime State
 ├─ current passage
 ├─ variables
 ├─ visit history
 └─ choices
```

## 중요한 시스템

### 1. Story Map

분기 구조를 텍스트가 아니라 그래프로 보여 준다.

유용한 표시:
- 시작 passage
- 연결선
- 빈 passage
- 깨진 링크
- 태그 색상
- 검색

### 2. Authoring과 runtime의 구분

저작 화면에서 구조를 만들고, story format이 실행 규칙을 담당한다.

### 3. 상태 기반 이야기

단순한:

```text
A → B / C
```

뿐 아니라 과거 선택에 따라 이후 상황이 달라지는 이야기를 표현할 수 있다.

## 설계적으로 유용한 점

특히 중요한 것은 **분기형 콘텐츠를 tree가 아니라 graph로 보는 관점**이다.

```text
A
├─ B ─┐
└─ C ─┴→ D
```

같은 합류 구조를 자연스럽게 표현할 수 있다.

또한 오류를 목록으로만 보여주는 것이 아니라 **그래프 구조 자체에서 발견**하게 할 수 있다.

## 장점

- 전체 분기 구조를 이해하기 쉽다.
- 콘텐츠 제작자가 이야기 구조를 빠르게 점검할 수 있다.
- 반복·합류·조건 분기처럼 실제 이야기 구조를 표현하기 좋다.
- 저작과 실행을 분리하는 사고방식을 제공한다.

## 단점·주의

- 자유도가 높아질수록 초보자에게 조건/변수 개념이 어렵다.
- story format별 문법을 노출하면 교육용 서비스에서는 복잡해질 수 있다.
- 임의 JavaScript/매크로 실행 방식은 보안과 무결성 측면에서 그대로 가져오기 어렵다.
- cycle을 무조건 오류로 보거나 무조건 허용해서도 안 된다.
- 놀스토리는 이미 Twine보다 구체적인 StoryDocument/Runtime 구조가 있으므로 엔진 자체를 다시 만들 필요가 없다.

## 참고 가치가 큰 서비스

- 놀스토리 저작 화면
- 놀히스토리의 역사 시나리오가 분기형으로 결정될 경우
- 선택 기반 탐구 활동
- 상태와 경로가 중요한 교육 콘텐츠

---

# 4. Artsteps

## 한 줄 요약

**3D 공간을 '걷는 기술'보다 작품을 어떻게 배치하고 어떤 순서와 시점으로 감상시키는가에 초점을 둔 참고 사례.**

## 핵심 개념 구조

공개적으로 확인 가능한 수준에서:

```text
3D Space
   ↓
Artifact placement
   ↓
Guide point / narrative
   ↓
Visitor experience
   ↓
Share / Embed
```

내부 구현 소스는 공개되어 있지 않으므로 정확한 카메라·저장 알고리즘까지 단정하지 않는다.

## 중요한 시스템

### 1. Artifact와 공간의 분리

작품 자체와 작품이 놓인 공간상의 위치를 별개 개념으로 본다.

### 2. Guide point

3D 자유 이동만 제공하는 것이 아니라 특정 감상 지점이나 순서를 안내할 수 있다는 점이 중요하다.

### 3. Viewer experience

전시 제작 기능과 방문자가 감상하는 기능의 목적이 다르다.

## 설계적으로 유용한 점

3D 전시실을 만들 때 흔히:

- 캐릭터
- 걷기
- 점프
- 조명
- 재질

부터 구현하기 쉽다.

하지만 실제 관람 품질은:

```text
작품을 쉽게 찾는가
→ 적절한 거리에서 보는가
→ 다음 작품으로 자연스럽게 가는가
→ 설명을 놓치지 않는가
```

가 더 중요할 수 있다.

따라서 다음 모델이 유용하다.

```text
ArtworkTransform
Viewpoint
 ├─ eye
 └─ lookAt

Tour
 ├─ orderedArtworkIds
 └─ currentIndex
```

## 장점

- 자유 이동이 어려운 사용자도 전시를 볼 수 있다.
- 작품 중심으로 3D UX를 설계하기 좋다.
- 모바일·태블릿에서 조작 부담을 줄일 수 있다.
- 수업용 전시에서 교사가 의도한 관람 순서를 만들 수 있다.

## 단점·주의

- 3D 공간 자체는 저사양 기기에 부담이 될 수 있다.
- 자유 이동과 guided tour가 동시에 있으면 UX가 복잡해질 수 있다.
- 카메라 이동이 과하면 멀미를 유발할 수 있다.
- 3D만 제공하면 접근성이 떨어진다.
- 반드시 2D 작품 목록이나 대체 탐색 경로가 필요하다.

## 참고 가치가 큰 서비스

- 놀아트 3D 전시실
- 학생 작품 전시
- 가상 박물관
- 역사 공간 탐방형 서비스

---

# 5. Excalidraw

## 한 줄 요약

**기능이 많은 편집기를 초보자가 다룰 수 있게 만들면서, 저장 목적에 따라 상태를 선별하는 구조가 강한 사례.**

## 핵심 구조

```text
Elements
+
AppState
+
BinaryFiles
    ↓
Storage policy
 ├─ browser
 ├─ export
 └─ server
    ↓
Local persistence / collaboration
```

## 중요한 시스템

### 1. AppState 저장 정책

실제 소스의 `APP_STATE_STORAGE_CONF`는 각 상태 필드마다:

- browser에 저장할지
- export 파일에 넣을지
- server에 보낼지

를 별도로 정의한다.

예를 들어:
- zoom은 브라우저에서 기억할 수 있지만 작품 파일에는 필요 없다.
- grid 설정은 작품 속성이라면 export/server에도 포함할 수 있다.
- collaborators는 영속 작품 데이터가 아니다.

### 2. Elements와 BinaryFiles 분리

실제 LocalData 구조에서는:
- elements + 일부 app state
- binary files
를 다른 저장소로 다룬다.

### 3. Contextual UI

선택한 개체와 현재 작업에 맞는 기능을 보여 주는 방식이 초보자 편집기 설계에 유용하다.

## 설계적으로 유용한 점

tldraw의 lifecycle scope와 결합하면 다음과 같은 정책표를 만들 수 있다.

```text
field             browser export server realtime history
content              ✓      ✓      ✓       ✓       ✓
zoom                 ✓
selection            ✓                      후보
cursor                                      ✓
panel state          ✓
binary asset         ✓      ✓      ✓
```

## 장점

- 작품 데이터와 개인 UI 상태를 섞지 않기 좋다.
- export 파일이 불필요한 세션 정보로 오염되지 않는다.
- binary asset를 별도로 관리하기 쉽다.
- 초보자에게 복잡한 기능을 단계적으로 노출하기 좋다.
- 로컬 저장 실패나 quota 같은 브라우저 현실을 고려한 구조가 있다.

## 단점·주의

- excalidraw.com 서비스와 npm 패키지의 기능 범위를 혼동하면 안 된다.
- '기능을 숨기면 쉬워진다'는 보장은 없다.
- progressive disclosure를 과하게 쓰면 기능을 찾기 어려워질 수 있다.
- 실제 사용성은 과제 완료율로 검증해야 한다.
- hosted collaboration/E2EE 등을 패키지 기본 기능처럼 가정하면 안 된다.

## 참고 가치가 큰 서비스

- 놀아트 편집기
- 놀프린트 작업 UI
- 이미지·도형 기반 제작 도구
- export/import가 있는 로컬 편집 서비스

---

# 6. 다섯 도구를 비교하면

| 도구 | 가장 강한 영역 | 핵심 설계 질문 |
|---|---|---|
| tldraw | 상태 lifecycle | 이 데이터는 얼마나 오래 살아야 하는가? |
| Excalidraw | 저장 목적 분리 | 이 상태를 어디에 저장·공유해야 하는가? |
| H5P | 확장 가능한 콘텐츠 | 새 활동을 하나의 타입으로 정의할 수 있는가? |
| Twine | 구조적 저작 | 사용자가 전체 관계를 한눈에 이해할 수 있는가? |
| Artsteps | 관람 경험 | 사용자가 콘텐츠를 실제로 잘 찾아보고 감상하는가? |

이 다섯 질문은 이후 새로운 Knol 서비스를 분석하거나 설계할 때 공통 체크리스트로 사용할 수 있다.
