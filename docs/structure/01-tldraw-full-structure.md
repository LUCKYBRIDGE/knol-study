# tldraw — 공개 소스 기반 전체 구조도

검토 기준: 2026-11-08  
정확도: **높음(A1/A2)** — 공식 monorepo, package architecture 문서, SDK 문서 기준.

> 이 문서는 tldraw SDK와 tldraw.com hosted product를 구분한다. 공개 저장소에는 SDK뿐 아니라 dotcom app/worker도 포함되어 있지만, 배포 인프라의 모든 운영 세부가 공개되었다고 가정하지 않는다.

# 1. Product Boundary

```mermaid
flowchart TB
  U[User]
  U --> APP[tldraw application layer]
  APP --> UI[tldraw UI package]
  UI --> ED[Editor core]
  ED --> STORE[Record Store]
  STORE --> STATE[Reactive State]
  STORE --> SCHEMA[TL Schema + Migrations]
  ED --> ASSET[Asset Store]
  STORE --> PERSIST[Persistence adapter]
  STORE --> SYNC[Sync client]

  SYNC <--> ROOM[Sync room / server]
  ROOM --> STORAGE[Sync storage: memory / SQLite / DO wrapper]
  ASSET --> BLOB[Asset backend: memory / IndexedDB / object storage]

  APP -. hosted product .-> DOTCOM[tldraw.com app + workers]
```

핵심: **Editor가 곧 저장소가 아니다.** Editor는 Store 위에서 동작하고, Store는 Reactive State와 Schema를 이용하며, persistence/sync/asset은 별도 경계다.

# 2. Monorepo Topology

공식 monorepo 최상위 구조:

```text
tldraw/
├─ apps/
│  ├─ dotcom/                 hosted tldraw.com product
│  ├─ docs/
│  ├─ examples/
│  ├─ vscode/
│  ├─ analytics/
│  ├─ analytics-worker/
│  ├─ bemo-worker/
│  └─ mcp-app/
├─ packages/
│  ├─ state/                  reactive signals
│  ├─ state-react/            React bridge
│  ├─ store/                  typed record store
│  ├─ tlschema/               tldraw record schema + migrations
│  ├─ editor/                 editor engine + canvas primitives
│  ├─ tldraw/                 packaged end-user UI
│  ├─ sync-core/              sync protocol/client/server room/storage abstractions
│  ├─ sync/                   higher-level sync product integration
│  ├─ collaboration/          collaboration UI/features
│  ├─ assets/                 bundled/static assets helpers
│  ├─ commenting/
│  ├─ mentions/
│  ├─ collections/
│  ├─ driver/
│  ├─ validate/
│  ├─ utils/
│  └─ ...
├─ templates/                 integration starter templates
└─ internal/                  repository internal tooling
```

## 핵심 dependency chain

```mermaid
flowchart LR
  Utils --> State
  State --> Store
  Store --> TLSchema
  TLSchema --> Editor
  State --> Editor
  Store --> Editor
  Editor --> TldrawUI[tldraw package]
  TLSchema --> SyncCore
  Store --> SyncCore
  SyncCore --> Sync
```

실제 세부 package dependencies는 버전에 따라 변할 수 있으나 역할 경계는 위와 같다.

# 3. Reactive State Layer — @tldraw/state

공식 `packages/state/ARCHITECTURE.md`에서 직접 확인되는 핵심:

```text
Atom
 ├─ mutable signal
 └─ diff/history support

Computed
 ├─ derived signal
 └─ lazy invalidation

EffectScheduler
 └─ reaction/effect scheduling

capture
 └─ dependency graph

transactions
 ├─ batching
 ├─ rollback
 └─ effect deferral

HistoryBuffer
 └─ incremental diff history
```

## 내부 흐름

```mermaid
flowchart LR
  A[Atom change] --> TX[Transaction]
  TX --> DEP[Dependency invalidation]
  DEP --> C[Computed]
  C --> E[EffectScheduler]
  TX --> H[HistoryBuffer / diffs]
```

**설계 의미:** editor 렌더링과 데이터 변경을 수동 event bus로 연결하는 대신 reactive dependency graph를 사용한다.

# 4. Store Layer — @tldraw/store

Store의 기본 단위는 typed record.

```text
RecordType
 ├─ type name
 ├─ id rules
 ├─ default properties
 └─ validation

RecordStore
 ├─ put
 ├─ update
 ├─ remove
 ├─ get
 ├─ serialize / deserialize
 ├─ listen
 └─ mergeRemoteChanges
```

## 중요한 경계

```mermaid
flowchart TB
  RT[Record Types] --> RS[Record Store]
  RS --> SNAP[Snapshot / Serialized Store]
  RS --> LISTEN[Change Listener]
  LISTEN --> SAVE[Persistence]
  LISTEN --> SYNC[Sync]
  RS --> DERIVE[Derivation Cache]
```

Store change가 **렌더링·history·저장·동기화**의 공통 원천이 될 수 있다는 점이 중요하다.

# 5. TL Schema — 실제 record 종류

`packages/tlschema/src/records`에서 확인되는 핵심 record:

```text
TLDocument
TLPage
TLShape
TLBinding
TLAsset
TLCamera
TLInstance
TLPageState
TLPointer
TLPresence
TLComment
TLCustomRecord
TLUser
```

이를 의미별로 보면:

```mermaid
flowchart TB
  DOC[Document Scope]
  DOC --> Document
  DOC --> Page
  DOC --> Shape
  DOC --> Binding
  DOC --> Asset
  DOC --> Comment
  DOC --> CustomRecord

  SES[Session Scope]
  SES --> Instance
  SES --> PageState
  SES --> Camera
  SES --> Pointer

  PRE[Presence / Collaboration]
  PRE --> Presence
  PRE --> User
```

정확한 scope는 record 정의와 현재 schema에 의해 결정되므로 위 그룹은 이해를 위한 의미 구조다.

# 6. Editor Layer

`packages/editor/src/lib`:

```text
editor/
├─ TldrawEditor.tsx
├─ editor/              Editor class and state machine
├─ components/          canvas/editor components
├─ primitives/          geometry/render primitives
├─ hooks/
├─ config/
├─ exports/             export pipeline
├─ globals/
└─ utils/
```

Editor의 책임은 대략:

- tool/input 처리
- selection
- page/camera
- shape creation/update/delete
- geometry/query
- snapping
- history commands
- export
- UI가 호출할 명령 API

**Editor 데이터 자체를 영속화하는 것이 아니라 Store record를 변경한다.**

# 7. Asset System

공식 `TLAssetStore` 인터페이스:

```text
upload(asset, file)
resolve(asset, context)
remove(assetIds)
```

`TLAssetContext`는 screen scale, DPR, network effective type, original-resolution 요구 등을 전달할 수 있다.

```mermaid
flowchart LR
  File --> Upload[TLAssetStore.upload]
  Upload --> Meta[TLAsset record]
  Upload --> Blob[Binary/object storage]
  Meta --> Shape
  Render[Renderer] --> Resolve[TLAssetStore.resolve]
  Resolve --> Blob
```

중요: **TLAsset record와 실제 binary/blob 저장소는 같은 것이 아니다.**

기본/환경별 예:
- memory mode → data URL 가능
- persistenceKey → IndexedDB와 결합
- multiplayer → S3/R2 같은 object storage adapter 가능

# 8. Persistence

## Local

공식 persistence 기능은 `persistenceKey`를 통해 브라우저 IndexedDB에 document/assets를 보존하는 경로를 제공한다.

```mermaid
sequenceDiagram
  participant User
  participant Editor
  participant Store
  participant IDB as IndexedDB
  User->>Editor: edit
  Editor->>Store: record mutation
  Store-->>IDB: persist changes/snapshot
  Note over IDB: document + local assets
  User->>Editor: reload
  IDB-->>Store: restore
  Store-->>Editor: render restored document
```

## Snapshot

`TLStoreSnapshot`, `TLSerializedStore`로 직렬화/복원 가능.

# 9. Sync Architecture

`packages/sync-core/src/lib` 실제 구성:

```text
TLSyncClient
TLSyncRoom
TLSocketRoom
RoomSession
ClientWebSocketAdapter
ServerSocketAdapter
TLSyncStorage
InMemorySyncStorage
SQLiteSyncStorage
NodeSqliteWrapper
DurableObjectSqliteSyncWrapper
protocol
diff / recordDiff
chunk
```

## 구조

```mermaid
flowchart LR
  StoreA[Client Store A] <--> ClientA[TLSyncClient]
  StoreB[Client Store B] <--> ClientB[TLSyncClient]
  ClientA <--> WS1[WebSocket Adapter]
  ClientB <--> WS2[WebSocket Adapter]
  WS1 <--> Room[TLSocketRoom / TLSyncRoom]
  WS2 <--> Room
  Room <--> SS[TLSyncStorage]
  SS --> MEM[InMemory]
  SS --> SQL[SQLite]
  SS --> DO[Durable Object SQLite wrapper]
```

즉 실시간 협업은 단순히 같은 JSON을 덮어쓰는 구조가 아니다. client diff/protocol/room/session/storage 계층이 따로 있다.

# 10. Hosted tldraw.com Boundary

공개 monorepo의 `apps/dotcom`에는:

- client
- sync-worker
- asset-upload-worker
- image-resize-worker
- user-content worker
- zero-cache
- snapshot/restore tooling

등이 존재한다.

따라서 hosted product는 SDK 위에 **계정·파일·worker·asset pipeline·운영 기능**을 추가한 별도 계층으로 이해해야 한다.

```mermaid
flowchart TB
  SDK[tldraw SDK]
  SDK --> WebApp[dotcom client]
  WebApp --> SyncWorker
  WebApp --> AssetWorker
  AssetWorker --> ResizeWorker
  WebApp --> UserContentWorker
  WebApp --> Cache[zero-cache]
```

세부 배포 토폴로지와 production infrastructure 전체는 공개 코드만으로 단정하지 않는다.

# 11. Extension Points

- custom shapes
- custom bindings
- custom records
- tools
- UI overrides/components
- asset store
- user store
- persistence
- sync backend
- exports
- starter templates

# 12. 장점

1. **상태·저장·편집·동기화가 강하게 분리**되어 있다.
2. Record + Schema 기반이라 migration과 validation에 유리하다.
3. local-only에서 multiplayer까지 같은 core model을 활용한다.
4. asset lifecycle이 명시적이다.
5. editor를 headless-ish core처럼 확장하고 UI를 교체할 수 있다.
6. 큰 기능이 package 경계로 나뉘어 장기 유지보수성이 높다.

# 13. 단점 / 비용

1. 작은 앱에는 학습·구조 비용이 크다.
2. reactive state + typed store + schema + editor + sync를 이해해야 한다.
3. custom behavior가 많아질수록 SDK conventions에 종속된다.
4. hosted product 수준을 재현하려면 sync뿐 아니라 asset/worker/account 운영이 필요하다.
5. SDK 라이선스와 watermark/business 조건은 현재 LICENSE/README를 반드시 재확인해야 한다.

# 14. Knol에서 가져올 구조

가장 가치가 큰 것은 SDK 자체보다:

```text
Reactive state
      ↓
Versioned canonical store
      ↓
Editor commands
      ↓
Persistence / Sync adapters

+ document/session/presence lifecycle
+ independent asset store
```

이다.


# 15. 더 깊은 상태 수명주기 — Record Scope + Field Preservation

tldraw은 단순히 record를 document/session/presence로 나누는 데서 끝나지 않는다.

## RecordType scope

공식 Store 문서와 실제 record 정의:

```text
document scope
  persisted + synced

session scope
  per instance/tab
  persist될 수 있으나 sync 대상 아님

presence scope
  synced
  장기 persistence 대상 아님
```

실제:
- `TLDocument` → `scope: 'document'`
- `TLCamera` → `scope: 'session'`
- `TLPointer` → `scope: 'session'`
- `TLInstance` → `scope: 'session'`
- `TLInstancePresence` → `scope: 'presence'`

## TLInstance 안에서도 다시 보존 정책을 갖는다

`TLInstance.ts`에는 `shouldKeyBePreservedBetweenSessions`가 존재한다.

즉 session record라고 해서 모든 field를 다음 세션에 유지하지 않는다.

보존 예:
- focus mode
- debug mode
- tool lock
- export background
- grid mode
- screen/device 특성 일부
- readonly config

비보존 예:
- current page
- brush
- cursor
- scribble
- chat message
- hover state
- open menus
- duplicate temporary state
- camera moving state

따라서 tldraw의 상태 모델은 실제로:

```text
Level 1: record scope
  document / session / presence

Level 2: field lifecycle
  preserve across sessions?
  reset on load?

Level 3: persistence destination
  local / sync / snapshot
```

으로 이해하는 편이 정확하다.

# 16. Store 생성 경로

실제 `createTLStore.ts`:

```mermaid
flowchart TB
  Options[TLStoreOptions]
  Options --> Themes[resolve themes]
  Options --> Utils[shape/binding/asset utils]
  Utils --> Schema[createTLSchema]
  Schema --> Store[new Store]
  Options --> AssetStore[TLAssetStore]
  Options --> UserStore[TLUserStore]
  AssetStore --> StoreProps[TLStoreProps]
  UserStore --> StoreProps
  StoreProps --> Store
  Snapshot[snapshot] --> Load[loadSnapshot]
  Load --> Store
```

확장 가능 지점:
- custom schema
- shapeUtils
- bindingUtils
- assetUtils
- migrations
- custom records
- asset adapter
- user resolver
- collaboration status/mode
- onMount

이 구조는 '편집기 생성 시 모든 서비스 기능을 Editor class에 직접 주입'하는 방식보다 훨씬 결합도가 낮다.

# 17. Sync의 실제 책임 분해

## Client

`TLSyncClient`:
- local Store와 socket 사이 diff 송수신
- server clock 추적
- protocol version
- presence mode
- network scheduling
- remote error handling
- reconnect 가능한 persistent socket contract

공식 소스에서 solo mode와 collaborative mode의 network FPS도 별도로 둔다.

## Server Room

`TLSocketRoom`:
- document별 authoritative room
- WebSocket lifecycle
- session map
- presence
- message chunking
- readonly/session access
- record authorizer
- storage adapter
- committed changes callback
- hibernation용 session snapshot/resume

## Sync Storage

```text
TLSyncStorage
├─ InMemorySyncStorage
├─ SQLiteSyncStorage
├─ NodeSqliteWrapper
└─ DurableObjectSqliteSyncWrapper
```

따라서 실제 구조는:

```mermaid
flowchart LR
  Editor --> LocalStore
  LocalStore --> SyncClient
  SyncClient --> Socket
  Socket --> SocketRoom
  SocketRoom --> SyncRoom
  SyncRoom --> SyncStorage
  SyncRoom --> PresenceStore
  SocketRoom --> SessionSnapshot[session snapshot/resume]
```

# 18. Sync 오류 모델

실제 client/server contract에는 다음 fatal reason이 정의되어 있다.

- NOT_FOUND
- FORBIDDEN
- NOT_AUTHENTICATED
- UNKNOWN_ERROR
- CLIENT_TOO_OLD
- SERVER_TOO_OLD
- INVALID_RECORD
- RATE_LIMITED
- ROOM_FULL
- MESSAGE_TOO_LARGE

즉 협업 기능을 설계할 때 단순 '연결 실패' 하나로 처리하지 않고 **권한·버전·데이터·용량·수용량 오류를 분리**하는 것이 중요하다.

# 19. 서버 측 권한 구조

현재 `TLSocketRoom`에는:
- readonly session
- object type 별 access
- record create/update/delete authorizer
- session metadata

같은 제어 지점이 있다.

이는 협업이 커지면:

```text
Can enter room?
Can edit document?
Can write specific object type?
Can create/update/delete this record?
```

를 별도 권한으로 나눌 수 있음을 보여준다.

# 20. 소스 근거

- `packages/state/ARCHITECTURE.md`
- `packages/store/src/lib/Store.ts`
- `packages/store/src/lib/StoreSchema.ts`
- `packages/store/src/lib/RecordType.ts`
- `packages/tlschema/src/records/TLDocument.ts`
- `packages/tlschema/src/records/TLInstance.ts`
- `packages/tlschema/src/records/TLPresence.ts`
- `packages/editor/src/lib/config/createTLStore.ts`
- `packages/sync-core/src/lib/TLSyncClient.ts`
- `packages/sync-core/src/lib/TLSocketRoom.ts`
- `packages/sync-core/src/lib/TLSyncRoom.ts`

이 문서의 구조 설명은 위 공개 source를 기준으로 작성했다.
