# MakeCode / PXT — 페이지·UI·UX·버튼

MakeCode는 target별 브랜딩과 도구 차이가 있으므로 "모든 MakeCode에서 항상 동일"하다고 단정하지 않는다.

## 1. Home / Projects
- 새 프로젝트
- 기존 프로젝트
- search/filter
- import
- tutorials/examples
- target별 cards

## 2. Editor
주요 영역:

```text
Header
Simulator
Toolbox
Code Workspace
Editor Toolbar
Side/Dialogs
```

### Editor modes
- Blocks
- JavaScript/TypeScript
- Python(target 지원 시)

## 3. Block Toolbox
API annotation에서:
- category
- group
- Advanced
- More...
- block ordering
이 결정된다.

### Progressive disclosure
- category `advanced=true`
- block `advanced=true`
- expandable arguments

고급 기능을 삭제하지 않고 기본 UI에서 한 단계 숨긴다.

## 4. Editor Toolbar
source에서 확인되는 주요 행동:
- project rename
- Undo / Redo
- zoom
- save
- GitHub(target 조건)
- Download
- download options
- Connect Device
- Disconnect
- Choose Hardware
- Download as File
- Help

## 5. Simulator Toolbar

실제 `simtoolbar.tsx`:
- Start / Stop
- Restart
- Debug
- Fullscreen
- Mute/Unmute
- keyboard shortcuts
- collapse/show simulator
- simulator console
- device console
- Screenshot
- assembly instructions(target 조건)

## 6. Extension UI
Extensions Browser:
- extension 검색
- 설치
- 설치 후 toolbox category 자동 확장

## 7. Specialized editors
target에 따라:
- Tilemap
- Sprite
- Music
- Piano Roll
- Sound Effect
- Image/Asset
가 block field/editor와 연결된다.

## 8. Tutorial
- side-by-side instruction
- step validation
- 제한된 toolbox
- simulator
를 조합한다.

## UX 핵심
초보자용 Blocks에서 시작해 같은 프로젝트를 text code, debugger, hardware까지 확장하는 **성장 사다리**가 있다.
