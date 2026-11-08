# H5P — 화면·UI·UX·컨트롤

H5P는 host-dependent이므로 특정 URL route를 H5P 공통으로 단정할 수 없다.

## 공통적으로 존재하는 surface

### 1. Content Type 선택
Hub/host selector에서 activity type 선택.

예:
- Multiple Choice
- Interactive Video
- Course Presentation
- Branching Scenario
등.

### 2. Authoring Editor
`semantics.json`을 기반으로 form 생성.

주요 control type:
- text
- textarea
- number
- boolean
- select
- group
- list
- nested library selector
- image/file
- audio/video
- coordinates/dimensions
- metadata
- rich HTML

### 3. Type-specific Authoring
Interactive Video 같은 복잡한 유형은 generic form만 쓰지 않고 전용 editor dependency/widget를 사용한다.

### 4. Player / Runtime
학생/사용자가 실제 activity 수행.

activity별:
- 선택
- 제출
- 다시 시도
- 해설/feedback
- 다음 화면
- score
등 UI는 content type이 결정한다.

### 5. Import / Export
host가 제공하는 H5P package upload/download UI.

### 6. Metadata / Library 관리
host/admin 권한에 따라 library install/update 화면이 별도일 수 있다.

## UX의 핵심

### 반복 UI
schema에서 자동 생성.

### 특수 UI
custom widget.

### 학생 화면
authoring dependency 없이 runtime dependency만 사용 가능.

즉 **저작자 UI와 학습자 UI를 구조적으로 분리**한다.

## 버튼을 제품 공통으로 고정하면 안 되는 이유

"저장", "추가", "업로드", "다운로드" 같은 shell control은 Moodle/WordPress/H5P.com 등 host가 다르게 제공할 수 있다.

따라서 분석에서는:
- H5P 공통 widget
- host UI
- content type runtime UI
를 구분한다.
