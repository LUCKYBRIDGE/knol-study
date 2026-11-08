# Twine — 저장소·파일 구조

```text
src/
├─ app.tsx
├─ routes/
│  ├─ welcome/
│  ├─ story-list/
│  ├─ story-edit/
│  ├─ story-play/
│  ├─ story-proof/
│  └─ story-test/
├─ components/
│  ├─ passage/
│  │  ├─ passage-map/
│  │  └─ passage-connections/
│  ├─ story/
│  ├─ story-format/
│  ├─ tag/
│  └─ fuzzy-finder/
├─ dialogs/
│  ├─ passage-edit/
│  ├─ story-details/
│  ├─ story-formats/
│  ├─ story-import/
│  ├─ story-search
│  ├─ story-javascript
│  └─ story-stylesheet
├─ store/
│  ├─ stories/
│  ├─ undoable-stories/
│  ├─ persistence/
│  │  ├─ local-storage/
│  │  └─ electron-ipc/
│  ├─ story-formats/
│  └─ prefs/
├─ electron/
└─ util/
```

## Story edit toolbar

```text
routes/story-edit/toolbar/
├─ story-edit-toolbar.tsx
├─ undo-redo-buttons.tsx
├─ zoom-buttons.tsx
├─ passage/
└─ story/
```

## 테스트

- Passage connection
- Story Map
- fuzzy finder
- dialogs
- route
- storage quota
- Electron process
- accessibility via jest-axe
- smoke E2E
