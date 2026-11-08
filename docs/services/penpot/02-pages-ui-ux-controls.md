# Penpot — 페이지·UI·UX·버튼

Penpot 현재 frontend는 query-string의 `screen` 값으로 주요 screen을 구분한다.

## 1. Auth

- Login
- Register
- Register validation/success
- Recovery request
- Recovery
- Verify token

## 2. Settings

실제 route name:
- Profile
- Password
- Feedback
- Options
- Subscription
- Integrations
- Notifications
- Shortcuts

## 3. Dashboard

실제 route:
- Recent
- Search
- Files
- Libraries
- Fonts
- Font Providers
- Members
- Invitations
- Webhooks
- Settings
- Deleted

### UX 의미
디자인 작업 자체와 team/library/admin 자원을 workspace 밖 dashboard에서 분리한다.

## 4. Workspace
핵심 design editor.

query params:
- team-id
- file-id
- page-id
- layout

### 주요 영역
- canvas
- layers/tree
- pages
- properties/options sidebar
- assets/libraries
- component controls
- prototype/interactions
- comments
- zoom/navigation

### 속성 UI
shape type별 options:
- Rect
- Circle
- Image
- Text
- Group
- Path/SVG 등

### Multiple selection
서로 다른 shape를 같이 선택하면:
- 모든 object가 같은 값 → 실제 값 표시
- 서로 다름 → `:multiple`
- 지원 가능한 shape에만 변경 적용

이 UX는 복잡한 다중 선택 편집을 안전하게 만든다.

## 5. Viewer
실제 viewer params:
- file-id
- page-id
- frame-id
- section
- interactions-mode
- index
- share-id

즉 편집 workspace와 published/prototype viewing을 분리한다.

## 6. Frame Preview
특정 frame 빠른 preview.

## 7. Inspect
개발자 handoff:
- Info
- Code

Code generation:
- SVG
- HTML
- CSS

## 8. Component UX

### Main Component
원본.

### Instance/Copy
원본 변경을 따라감.

### touched override
instance에서 특정 속성군을 수정하면 해당 group은 이후 main update 전파에서 제외.

이 방식은 "원본을 따라가면서도 일부만 사용자화"하는 문제를 해결한다.

## 9. 주요 UI 철학

- 실제 웹 layout 개념인 Flex/Grid 사용
- object type별 속성 UI
- multiple mixed state 명시
- workspace/viewer/dashboard 분리
- developer Inspect를 별도 mode로 제공
- design system/component/token을 독립 자산으로 관리
