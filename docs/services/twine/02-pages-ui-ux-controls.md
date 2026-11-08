# Twine — 페이지·UI·UX·버튼

## 실제 routes

| Route | 화면 |
|---|---|
| `/` | Story List |
| `/welcome` | Welcome |
| `/stories/:storyId` | Story Edit |
| `/stories/:storyId/play` | Play |
| `/stories/:storyId/proof` | Proof |
| `/stories/:storyId/test` | Test |
| `/stories/:storyId/test/:passageId` | 특정 passage부터 Test |

HashRouter를 사용한다.

## Story List

핵심 행동:
- 새 Story
- Story 열기
- Import
- rename/delete 등 story 관리
- Story Format 관리
- Preferences

## Story Edit

### 중심
Story Map.

Passage card를 공간에 배치하고 link 관계를 선으로 본다.

### toolbar
- 새 Passage
- Story 관련 menu
- Undo / Redo
- Zoom in/out/reset 계열
- Search/Fuzzy navigation

### Passage
- double click/edit
- rename
- tags
- content text
- 연결선

## Dialogs

실제 source:
- Passage Edit
- Passage Tags
- Story Details
- Story Formats
- Story Import
- Story Search
- Story JavaScript
- Story Stylesheet
- Story Tags
- App Preferences
- About

## 구조 진단 UI

- start passage marker
- broken connection
- self connection
- empty passage
- tag styling
- reference link

### UX 핵심
오류를 별도 validation report에만 두지 않고 **Story Map 자체에 표시**한다.

## Play/Test/Proof 분리

- Play: 최종 독자 경험에 가까움
- Test: 개발 중 특정 상태/Passage 확인
- Proof: text 중심 검토

하나의 preview 버튼으로 모든 검토 목적을 해결하지 않는다.
