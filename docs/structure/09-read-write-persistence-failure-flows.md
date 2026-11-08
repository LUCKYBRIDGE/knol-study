# 쓰기·읽기·저장·실패 흐름 비교

## 1. tldraw — local edit

```mermaid
sequenceDiagram
  participant U as User
  participant E as Editor
  participant S as Store
  participant R as Reactive State
  participant P as Persistence
  U->>E: pointer/tool action
  E->>S: record put/update/remove
  S->>R: atom/diff change
  R-->>E: derived/render update
  S-->>P: document change
  P->>P: IndexedDB/snapshot persistence
```

### 실패 지점
- invalid record/schema
- migration incompatibility
- asset upload failure
- IDB/quota/storage failure

## 2. tldraw — multiplayer

```mermaid
sequenceDiagram
  participant E as Editor/Store
  participant C as TLSyncClient
  participant W as WebSocket
  participant R as TLSocketRoom
  participant ST as TLSyncStorage
  E->>C: local diff
  C->>W: protocol push
  W->>R: message
  R->>R: validate/authorize/apply
  R->>ST: authoritative persistence
  R-->>W: server diff/ack
  W-->>C: update
  C-->>E: merge remote changes
```

### 명시된 fatal failure
권한/인증/버전/invalid record/rate limit/room capacity/message size 등.

---

# 3. Excalidraw — local edit

```mermaid
sequenceDiagram
  participant U as User
  participant A as App
  participant S as Element Store
  participant H as History
  participant L as LocalData
  participant LS as localStorage
  participant IDB as IndexedDB

  U->>A: action
  A->>S: commit elements/appState
  S->>S: durable/ephemeral classify
  S-->>H: durable delta
  A-->>L: save state
  L->>L: debounce + policy filter
  L->>LS: elements + appState
  L->>IDB: BinaryFiles
```

### 실패 지점
- localStorage quota
- IndexedDB file write error
- binary file missing
- history delta conflicts
- browser lifecycle before debounce flush

---

# 4. Excalidraw — collaboration

```mermaid
sequenceDiagram
  participant A as App
  participant P as Portal
  participant WS as Socket
  participant F as Firebase
  participant B as Other Client

  A->>P: durable scene update
  P->>WS: encrypted/broadcast data
  WS-->>B: realtime update
  A->>F: encrypted scene transaction
  A->>F: binary file upload
  F-->>A: stored scene/version
```

핵심: **realtime 전달과 durable 저장이 별개**.

---

# 5. H5P — author/save

```mermaid
sequenceDiagram
  participant A as Author
  participant E as H5P Editor
  participant S as Semantics
  participant H as Host
  participant C as Core
  participant DB as Host Storage

  A->>E: edit activity
  S-->>E: field/widget contract
  E->>H: params/assets submit
  H->>C: validate/save
  C->>C: semantic/content validation
  C->>DB: content + library dependencies + files
```

### 실패 지점
- invalid package
- unsupported/missing library
- invalid semantics data
- whitelist violation
- dependency/version mismatch
- file storage error

---

# 6. H5P — runtime

```mermaid
sequenceDiagram
  participant Page as Host Page
  participant C as H5P Core
  participant Lib as Libraries
  participant R as Runtime
  participant Host as Host/LMS

  Page->>C: render content id
  C->>Lib: resolve preloaded dependencies
  C-->>Page: scripts/css + params
  Page->>R: instantiate content type
  R-->>Host: xAPI/events/state where supported
```

---

# 7. Twine — authoring

```mermaid
sequenceDiagram
  participant U as Author
  participant UI as Story Map/Passage UI
  participant S as Stories Store
  participant UDO as Undo Wrapper
  participant P as Persistence

  U->>UI: edit passage
  UI->>S: action
  S->>UDO: reducer/update
  UDO-->>S: reversible state
  S->>P: persistable change?
  P->>P: localStorage/Electron adapter
```

### 특징
selected/highlighted 등은 persist filter에서 제외.

---

# 8. Twine — publish

```mermaid
sequenceDiagram
  participant U as Author
  participant H as usePublishing
  participant S as Story Store
  participant F as Story Format Store
  participant P as publishStoryWithFormat
  participant O as HTML

  U->>H: publish
  H->>S: load story
  H->>F: load named format/version
  F-->>H: format source
  H->>P: story + format source
  P-->>O: standalone HTML
```

### 실패 지점
- missing format
- incompatible/unloadable format
- malformed story/import
- browser local storage loss

---

# 9. Artsteps/Metasteps — product flow

확인 가능한 수준:

```mermaid
flowchart LR
  Creator --> Space
  Creator --> Artifact
  Space --> Placement
  Artifact --> Placement
  Placement --> Guide
  Guide --> Publish
  Publish --> Visitor
  Visitor --> ShareSocial[Share / social]
```

Metasteps 진화:

```text
Space
  + Portal graph
  + Info Points
  + multiple Guided Paths
  + Webviews
  + Membership/Roles
  + District
```

실제 save API, database write, scene serialization은 Unknown.
