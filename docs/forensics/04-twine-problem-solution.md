# Twine — 비선형 이야기의 복잡성을 초보자에게 숨기는 방식

검토일: 2026-11-08  
소스 기준: 공식 `klembot/twinejs` develop, package version 2.12.1

# 1. Twine의 핵심 문제

비선형 이야기는 본질적으로 어렵다.

작가는 동시에:
- 장면 내용
- 장면 간 연결
- 시작점
- 반복
- 합류
- 변수/조건
- story runtime
을 생각해야 한다.

Twine은 이를 프로그래밍 IDE처럼 보여주지 않고 **Passage + Story Map**이라는 시각적 메타포로 단순화한다.

# 2. 가장 중요한 UX 결정: "기본 이야기는 코드 없이"

공식 Design Goals:
- basic link 사용법을 짧게 설명할 수 있어야 함
- beginners 우선
- sensible defaults
- undo 가능
- jargon 회피
- icon-only button 최소화
- discoverability
- mode 최소화
- touch 지원
- keyboard/screen reader

## 핵심

고급 기능을 없앤 게 아니다.

```text
기본: passage + link
고급: story format + variables + conditions + JS/CSS
```

으로 진입 순서를 만든다.

# 3. 복잡한 graph를 "Passage 카드"로 바꾼다

Passage:
- name
- text
- tags
- position
- size

Story Map:
- card
- arrow
- self loop
- broken link
- start marker
- tag stripe
- search

사용자는 graph theory를 몰라도:
- 장면 배치
- 연결
- 끊김
을 눈으로 이해한다.

# 4. Link 작성이 곧 Graph editing

`[[Passage]]` 문법을 쓰면 Story Map 연결이 만들어진다.

Twine은 text authoring과 graph editing을 별도 작업으로 완전히 나누지 않는다.

```text
Passage text
   ↓ parse link
Graph edge
```

## 자동 생성

새 destination link를 입력했는데 passage가 없으면 empty passage를 자동으로 만든다.

link를 삭제하면 조건에 따라 자동 생성 empty passage를 정리한다.

### 이게 해결하는 문제

사용자가:
1. node 생성
2. 이름 입력
3. edge 연결
4. 기존 passage로 돌아감

을 반복하지 않게 한다.

**글을 쓰는 행동 자체가 구조 편집이 된다.**

# 5. 오류는 별도 validation 화면보다 Story Map에 드러낸다

- broken link → 빨간 선 + no-entry
- empty passage → 반투명/dotted
- start → rocket
- self-link → circular arrow
- format reference → dashed line

즉 구조의 이상을 **구조 위에서 바로 표현**한다.

# 6. 검색은 정확 일치가 아니라 Fuzzy Finder

Story Map이 커지면 spatial navigation만으로 부족하다.

Twine은:
- passage name
- passage body
를 fuzzy search한다.

name match에 조금 더 높은 우선순위를 준다.

## 문제 해결

작가가 카드 위치를 기억하지 못해도 text 기억으로 장면을 찾는다.

# 7. Story 데이터에 일부 UI 상태를 함께 두는 실용주의

Passage:
- selected
- highlighted
- position/size

Story:
- selected
- zoom
- snapToGrid

tldraw처럼 domain/session record를 엄격하게 나누지 않는다.

대신 persistence 단계에서:
- selected
- highlighted
- lastUpdate 일부
같은 trivial change를 저장 대상 변경으로 보지 않는다.

## 장점
구조 단순.

## 단점
domain/editor-state 경계가 약함.

# 8. Browser와 Desktop 차이는 persistence adapter로 흡수

```text
Stories / Prefs / Formats
        ↓
Persistence hook
   ┌────┴─────┐
browser      Electron
localStorage  IPC/files
```

제품 UX는 최대한 같게 유지하고 저장 경로만 다르게 한다.

# 9. authoring runtime과 published runtime을 완전히 분리

Twine 자체는 게임 엔진을 하나로 고정하지 않는다.

```text
Story data
   +
Selected Story Format
   ↓ publish
Standalone HTML
```

Story Format이 runtime semantics를 정한다.

## 결과

같은 authoring application으로 서로 다른 interactive fiction runtime을 사용할 수 있다.

# 10. publish 시점에 format을 binding

`usePublishing()`:
- story 조회
- story format name/version 조회
- format source load
- `publishStoryWithFormat()`

publish는 `{{STORY_DATA}}` 위치에 Twine story markup을 결합한다.

## 중요

authoring app 내부 runtime state가 그대로 배포되는 것이 아니다.

# 11. portable identity와 editor identity를 분리

Story:
- id: 현재 app 내부 GUID
- IFID: import/export 후 유지되어야 하는 작품 식별

## 유용한 원리

```text
현재 편집 세션의 객체 ID
≠
콘텐츠의 장기 portable identity
```

복사/내보내기/재가져오기 시스템에 매우 유용하다.

# 12. Extension은 Story Format 안에 제한

Story Format은:
- runtime source
- version
- CodeMirror mode
- toolbar
- command
- reference parser

등을 확장할 수 있다.

하지만:
- standalone arbitrary plugin은 아님
- extension disable 가능
- runtime에 필수여서는 안 됨
- Twine version range 명시

즉 core 확장성을 허용하면서 **extension blast radius를 제한**한다.

# 13. Cycle을 오류로 보지 않는다

공식 Reference는 self-link를 정상적으로 원형 화살표로 표시한다.

왜냐하면 interactive fiction에서:
- 장소 재방문
- 반복
- 상태 변화 후 다시 방문
은 정상적인 구조다.

따라서 graph validator는 단순 cycle detection보다 **runtime 의미를 고려한 위험 분류**가 필요하다.

# 14. 저장 손실 문제를 제품 차원에서 인정

Design Goals는 browser Twine의 local storage 특성과 Safari 제약으로 작업 손실 위험이 있음을 명시한다.

또 startup에서 story 상태를 확인하고 가능한 경우 repair하는 경로가 있다.

## 교훈

local-first라고 해서 "자동으로 안전"한 것이 아니다.

사용자에게:
- backup/export
- storage quota
- repair
경로가 필요하다.

# 15. 테스트가 보여주는 Twine의 실제 UX 우선순위

컴포넌트 테스트가 매우 세밀하다.

특히:
- broken connection
- self connection
- start connection
- passage map
- marquee selection
- fuzzy finder
- dialog stack
- mobile/touch 관련 control
- storage quota
를 검증한다.

여러 component test에서 `jest-axe`로 accessibility violation을 검사한다.

## 의미

Twine은 "graph 계산 정확도"뿐 아니라 **그 구조가 접근 가능한 UI로 표시되는가**를 테스트한다.

# 16. 핵심 trade-off

### 얻는 것
- 초보자가 빠르게 시작
- graph 전체 구조 시각화
- author/runtime 분리
- story format 생태계
- HTML portability
- 높은 접근성 의식

### 지불하는 것
- story format별 문법 차이
- 고급 기능 일관성 부족
- domain/UI state 일부 혼합
- browser local persistence 위험
- runtime semantics를 Twine만 보고 완전히 분석할 수 없음

# 17. 가장 재사용 가치가 큰 원리

1. **복잡한 graph 개념을 사용자 언어인 카드/장면으로 번역한다.**
2. **콘텐츠 입력 자체가 구조 편집이 되게 한다.**
3. **오류를 구조 위에서 직접 시각화한다.**
4. **작은 규모는 spatial navigation, 큰 규모는 fuzzy search로 보완한다.**
5. **기본과 고급 기능의 진입 난도를 분리한다.**
6. **authoring과 published runtime을 분리한다.**
7. **portable identity를 local ID와 분리한다.**
8. **접근성을 별도 후처리가 아니라 컴포넌트 테스트 대상으로 둔다.**
