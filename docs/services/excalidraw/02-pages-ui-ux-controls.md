# Excalidraw — 페이지·UI·UX·버튼

Excalidraw hosted app은 tldraw처럼 다수 document route가 중심이라기보다 **단일 편집 화면에 modal/sidebar/menu를 조합**하는 구조가 강하다.

## 1. Main Canvas

핵심 영역:
- canvas
- main toolbar
- shape/property actions
- sidebar
- main menu
- collaboration users
- zoom/navigation
- dialogs/toasts

## 2. Main Menu — source 확인

`AppMainMenu.tsx`:

- Load scene
- Save to active file
- Export
- Save as image
- Live collaboration
- Command palette
- Search
- Help
- Clear canvas
- Excalidraw+
- Social links
- Sign in / Sign up
- Preferences
- Theme
- Language
- Canvas background

이 목록은 hosted app과 core default menu를 구분해서 봐야 한다.

## 3. Toolbar

주요 제작 도구:
- Selection
- Rectangle
- Diamond
- Ellipse
- Arrow
- Line
- Draw
- Text
- Image
- Eraser
- Frame
- Embeddable
- Laser
- Hand 등

일부 버전에서는 flowchart/sticky/AI 도구가 context에 따라 제공된다.

## 4. Contextual Shape Actions

선택한 object 종류에 따라:
- stroke
- background
- fill style
- stroke width/style
- opacity
- roundness
- font
- alignment
- arrowhead
등이 달라진다.

### UX 해법
처음부터 모든 속성을 보여주지 않고 **선택된 element에 유효한 것만 노출**한다.

## 5. Sidebar / Library

- reusable library items
- search
- drag/drop
- publish library 관련 UI

## 6. Dialogs

source에 실제 존재:
- Export / Image Export / JSON Export
- Help
- Confirm
- Element Link
- Paste Chart
- TTD(Text to Diagram)
- Overwrite Confirm
- Error

## 7. Mobile

`MobileMenu.tsx`, mobile toolbar가 별도 존재한다.

desktop UI를 그대로 축소하기보다 화면 폭에 맞춰 menu/tool 위치를 재구성한다.

## 8. UX 특징

- 첫 진입에서 바로 canvas
- 별도 계정 없이 local creation 가능
- shortcut/API/UI가 같은 Action을 공유
- context-based property exposure
- command palette/search로 기능 증가 문제를 보완
- live collaboration을 별도 modal/action으로 진입
