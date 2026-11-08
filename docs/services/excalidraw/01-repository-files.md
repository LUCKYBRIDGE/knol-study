# Excalidraw — 저장소·파일 구조

```text
excalidraw/
├─ packages/
│  ├─ excalidraw/
│  │  ├─ actions/
│  │  ├─ components/
│  │  ├─ data/
│  │  ├─ scene/
│  │  ├─ renderer/
│  │  ├─ history.ts
│  │  └─ appState.ts
│  ├─ element/
│  │  └─ src/
│  │     ├─ store.ts
│  │     ├─ delta.ts
│  │     ├─ binding.ts
│  │     ├─ frame.ts
│  │     ├─ groups.ts
│  │     ├─ transform.ts
│  │     └─ ...
│  ├─ math/
│  └─ common/
├─ excalidraw-app/
│  ├─ App.tsx
│  ├─ components/
│  │  ├─ AppMainMenu.tsx
│  │  ├─ AppWelcomeScreen.tsx
│  │  ├─ AppSidebar.tsx
│  │  └─ AI.tsx
│  ├─ collab/
│  │  ├─ Collab.tsx
│  │  └─ Portal.tsx
│  ├─ data/
│  │  ├─ LocalData.ts
│  │  ├─ FileManager.ts
│  │  ├─ firebase.ts
│  │  ├─ tabSync.ts
│  │  └─ fileStatusStore.ts
│  ├─ share/
│  └─ tests/
└─ firebase-project/
```

## UI 기능 분해

`packages/excalidraw/components/App.*`:
- selection
- selectionTool
- text/textTool
- pan
- viewport
- wheel
- clipboard
- duplicate
- drawshape
- bucketFill
- flowchart
- toolDrag
- cursor

거대한 App을 완전히 micro-package로 나누지는 않았지만 기능군별 파일로 분해한다.

## 테스트

`packages/excalidraw/tests/`에:
- history
- reconcile
- restore
- export
- image
- binding
- selection
- move/rotate
- viewport
- library
- accessibility/interaction 관련 regression

hosted app에는 collab/Firebase test가 별도 존재한다.
