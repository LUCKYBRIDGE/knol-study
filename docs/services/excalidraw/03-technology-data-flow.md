# Excalidraw — 기술·데이터 흐름

## 기술

- TypeScript
- React
- Vite
- Jotai 일부 app state
- Canvas rendering
- localStorage
- IndexedDB
- Firebase
- realtime socket collaboration
- client-side encryption path

## 핵심 상태

```text
Elements
AppState
BinaryFiles
```

## 세 축

### 무엇인가
elements / UI state / binary.

### 어디 남기는가
browser / export / server.

### 이번 변화는 무엇인가
IMMEDIATELY / NEVER / EVENTUALLY.

## 명령

```text
Keyboard
UI
API
  ↓
ActionManager
  ↓
Action.perform
  ↓
Updater / Store
```

## History

```text
StoreSnapshot
 ↓ compare
StoreDelta
 ↓ inverse
HistoryDelta
 ↓
undo / redo
```

## local

```text
Elements + filtered AppState
 → localStorage

BinaryFiles
 → IndexedDB
```

## hosted collaboration

```text
Realtime
  Portal / Socket

Durable
  Firebase scene + file storage

Encryption
  room key 기반 client-side path
```

## reconcile

local/remote:
- id
- version
- versionNonce
- fractional order/index
를 비교해 convergence를 만든다.

## 핵심 판단

Excalidraw은 **상태 자체와 변경의 의미를 따로 분류**한다는 점이 중요하다.
