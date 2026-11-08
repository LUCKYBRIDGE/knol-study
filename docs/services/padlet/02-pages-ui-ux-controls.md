# Padlet — 페이지·UI·UX·버튼

## 1. Dashboard / Creation
- Board 생성
- Sandbox 생성
- template 선택
- 최근/공유 padlet 탐색

## 2. Board

### 핵심 버튼
- `+ Post`
- Share
- settings/customize 계열
- post의 `...` 메뉴
- comment/reaction controls

### + Post
공식 help:
- text
- image
- video
- audio
- link
- PDF/file
- other padlet
- drawing
등 다양한 content를 Post 하나에 넣는다.

### Draw
Board에서:
`+ Post → All tools → Draw`

Drawing UI:
- white/black background
- color 선택
- Pen
- Eraser
- Undo
- Redo
- Save

## 3. Layout
같은 Post collection을 여러 방식으로 배열.

핵심 UX:
- content creation과 layout strategy를 분리
- 사용자가 모든 좌표를 직접 정렬하지 않아도 됨
- 일부 format은 자동 정렬, 일부는 자유 배치

## 4. Share menu
상단 Share 버튼.

현재 공식 permission:
- No access
- Reader
- Commenter
- Writer
- Moderator
- Creator/관리자 계열(계정 종류/현재 정책에 따라 명칭 차이)

공유:
- copy link
- QR
- embed
- export
등.

2025 이후 privacy UI가 개편되었으므로 과거 "Secret" 명칭만 기준으로 문서를 만들지 않는다.

## 5. Sandbox

공식 toolbar:
- Draw
- Text
- Shapes
- Sticky notes
- pointer/presentation tools
- AI image
- video
- voice
- interaction testing
등.

### Play mode
편집과 소비를 분리한다.
Play에서는 실수로 object를 움직이지 않고 card를 순서대로 진행.

### Linking
drawing/shape/text/image 등을 click 가능한 navigation object로 만들고 다른 card로 이동.

### Card permission
card `...` → Manage access.
특정 card만 read-only 등으로 제한 가능.

## 6. UX 핵심
- "학생은 Post 하나 올린다"는 단순 mental model
- layout은 교사가 선택
- Share/permission은 상단에 집중
- Sandbox는 Board와 별도 interaction model
- Play mode로 authoring과 presentation을 구분
