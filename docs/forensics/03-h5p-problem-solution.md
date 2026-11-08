# H5P — 교육 콘텐츠 재사용 문제를 플랫폼 계약으로 푸는 방식

검토일: 2026-11-08  
근거: 공식 H5P Core/Editor/content type source + H5P developer documentation

# 1. H5P가 푸는 진짜 문제

H5P의 핵심 문제는 "퀴즈를 만드는 법"이 아니다.

다음 문제를 동시에 해결하려 한다.

- 다양한 LMS/CMS에서 같은 활동 실행
- 활동 타입을 계속 추가
- 콘텐츠를 다른 사이트로 이동
- authoring UI 제공
- runtime 제공
- library dependency
- version upgrade
- score/completion/xAPI
- media
- 다국어
- 보안
- host별 DB/file/auth 차이

해법은 거대한 한 앱이 아니라 **계약을 가진 생태계**다.

# 2. 플랫폼을 네 개의 핵심 계약으로 분리

```text
Host Contract
  H5PFrameworkInterface

File Contract
  H5PFileStorage

Type Contract
  library.json

Content Contract
  semantics.json
```

이 네 가지가 H5P 구조를 설명하는 핵심이다.

# 3. Host 차이는 Interface로 흡수

H5P Core는 WordPress/Moodle/Drupal DB에 직접 종속되지 않는다.

`H5PFrameworkInterface`가:
- content insert/update
- library load/save
- permissions
- file URL
- translation
- external HTTP
- messages/errors
등을 host에 요청한다.

## 문제 해결

```text
H5P logic
  ↓ common
Framework Interface
  ↓
WordPress / Moodle / Drupal / other
```

따라서 content type을 플랫폼별로 다시 만들지 않는다.

# 4. Content Type은 단순 component가 아니라 versioned module

`library.json`:
- machineName
- major/minor/patch
- runnable
- coreApi
- JS/CSS
- preloaded dependencies
- dynamic dependencies
- editor dependencies
- license

즉 activity는:

```text
ID
+ version
+ runtime code
+ editor code
+ dependencies
```

를 함께 가진 배포 단위다.

# 5. 데이터 구조와 저작 UI 문제는 semantics.json으로 연결

`semantics.json`은:
- field type
- optional/default
- list/group
- label/description
- importance
- widget
- nested library
- showWhen

등을 정의한다.

H5P Editor는 이를 읽고 generic form을 생성한다.

```text
semantics
   ↓
generic editor widgets
   ↓
content params JSON
   ↓
runtime validation
```

## "풀어낸 방식"의 핵심

일일이 각 활동에:
- input
- select
- list add/remove
- file upload
- image editor
를 다시 작성하지 않는다.

반복적인 authoring 문제를 **schema → generic editor**로 바꾼다.

# 6. 그러나 generic editor만 고집하지 않는다

Editor library/widget를 별도로 허용한다.

실제 editor:
- group
- list
- library selector
- image
- audio/video
- file
- HTML/CKEditor
- coordinates
- dimensions
- metadata
등을 갖는다.

복잡한 type은 custom editor dependency를 사용할 수 있다.

## trade-off

```text
반복 설정 → schema-generated UI
핵심 특수 UX → custom widget/editor
```

이 균형이 중요하다.

# 7. .h5p는 실행파일이 아니라 이식 가능한 패키지

표준 zip container 안에:
- h5p.json
- content/content.json
- media
- libraries
가 들어갈 수 있다.

중요한 점:
**content params와 실행 library dependency를 함께 이동할 수 있다.**

따라서 단순 JSON export보다 portable runtime 생태계에 가깝다.

# 8. 업로드 보안은 "콘텐츠와 코드의 신뢰 수준을 다르게" 본다

H5P security model의 핵심:

- 일반 content file은 whitelist 검사
- library에는 JS 등 실행 코드가 필요할 수 있음
- library update/install 권한은 trusted user에게 제한
- author HTML/text는 server-side sanitization
- file location도 안전하게 처리

## 매우 중요한 설계 원리

```text
사용자가 만든 콘텐츠 데이터
≠
실행 가능한 플러그인 코드
```

둘의 권한을 같게 취급하지 않는다.

# 9. Package Validator는 여러 단계에서 실패를 차단

실제 `H5PValidator::isValidPackage()`에서:
- ZipArchive 지원
- .h5p extension
- unzip 가능 여부
- 개별 파일 최대 크기
- 전체 압축 해제 크기
- hidden/system file skip
- content whitelist
- library whitelist
- content.json 존재
- h5p.json 존재
- JSON validation
- library directory naming
- library manifest
- missing dependencies
등을 검사한다.

## 의미

import는 "파일을 연 뒤 오류가 나면 처리"가 아니라 **저장 전에 검증 pipeline**을 통과한다.

# 10. Content validation은 semantics와 연결

`H5PContentValidator`는 semantics를 따라 content params를 검사한다.

따라서:
- authoring 입력
- imported content
- runtime에 전달할 params

가 같은 data contract에 묶인다.

# 11. Dependency는 용도별로 구분

### preloaded
runtime 시작 때 필요.

### dynamic
필요 시 불러올 수 있는 library.

### editor
저작할 때만 필요.

## 효과

학생 runtime에 authoring dependency를 모두 넣을 필요가 없다.

# 12. Runtime은 host page와 content library 사이의 boot layer

```text
Host page
  ↓
H5P Core JS
  ↓ dependency resolution
Content params + JS/CSS
  ↓
Content type runtime
  ↓
Events / xAPI / state
```

H5P Core가 content type을 mount하고 event를 host로 전달한다.

# 13. 학습 결과는 xAPI/event contract로 외부에 전달

H5P question contract는:
- score min/raw/max/scaled
- response
- definition
등을 xAPI에 표현할 수 있다.

## 장점
activity가 달라도 결과 전달 형식을 일정하게 만들 수 있다.

## 주의
activity의 내부 "게임 점수"와 교육 평가 의미는 동일하지 않을 수 있다.

# 14. Resume는 공통 저장 버튼이 아니라 type contract

`getCurrentState()`를 구현한 library는 resume 기능을 지원할 수 있다.

host integration이 resume를 켜면 이전 state를 constructor extras로 다시 제공한다.

즉:

```text
Content Type supports state
        +
Host stores/polls state
        =
Resume
```

모든 H5P에서 자동으로 되는 기능이 아니다.

# 15. Version Upgrade는 code와 content data를 함께 진화시킨다

각 content library는:
- version
- upgrades.js
를 가질 수 있다.

즉 library code가 바뀌면 오래된 params를 새 schema에 맞게 변환해야 한다.

H5P의 확장성이 큰 대신 **호환성 부채를 명시적으로 떠안는다.**

# 16. H5P가 테스트 대신 강하게 의존하는 부분

확인한 주요 core/editor/content type repositories에는 tldraw/Excalidraw/Twine처럼 눈에 띄는 대규모 자동 test tree가 없다.

따라서 공개 저장소 수준에서 볼 때 H5P의 안정성은 상당 부분:
- strict package validation
- semantics contract
- version/dependency metadata
- platform integrations
- content type별 유지보수
에 의존한다.

이것은 "테스트가 없다"는 전체 생태계 단정이 아니라 **검토한 주요 repository에서 자동화 테스트 자산이 상대적으로 덜 드러난다**는 의미다.

# 17. 핵심 trade-off

### 얻는 것
- LMS 독립적 content type
- 재사용
- import/export
- 다양한 activity 생태계
- generic authoring
- 결과 표준화

### 지불하는 것
- dependency graph
- version compatibility
- library trust/security
- host integration complexity
- generic editor UX 한계
- content type 유지보수 분산

# 18. 가장 재사용 가치가 큰 원리

1. **플랫폼 차이를 interface contract로 격리한다.**
2. **콘텐츠 타입을 versioned module로 본다.**
3. **data schema가 generic authoring과 validation을 동시에 지원하게 한다.**
4. **runtime dependency와 editor dependency를 구분한다.**
5. **사용자 콘텐츠와 실행 코드를 다른 신뢰 수준으로 다룬다.**
6. **import는 저장 전에 엄격하게 검증한다.**
7. **resume 기능은 타입별 state contract로 설계한다.**
8. **콘텐츠와 코드가 함께 진화하므로 migration이 필수다.**
