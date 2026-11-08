# Twine — 기술·데이터 흐름

## 기술

- TypeScript
- React
- React Router HashRouter
- reducer/context 기반 store
- browser local storage
- Electron IPC/files
- CodeMirror
- Story Format runtime
- HTML publication

## 데이터

```text
Story
├─ Passage[]
├─ startPassage
├─ StoryFormat name/version
├─ script
├─ stylesheet
├─ tags
└─ map view state 일부

Passage
├─ name
├─ text
├─ tags
├─ left/top
├─ width/height
└─ selection UI field 일부
```

## persistence

story object 안에 일부 UI field가 섞여 있지만 `persistable-changes.ts`에서 trivial UI change를 필터링한다.

## publish

```text
Story data
+
Story Format source
 ↓
publishStoryWithFormat
 ↓
Standalone HTML
```

## identity

- local app id
- IFID portable identity

를 구분한다.

## extension

Story Format:
- runtime
- CodeMirror mode
- toolbar
- command
- reference parser
를 제공할 수 있다.
