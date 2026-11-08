# tldraw — 기술·데이터·저장·동기화

## 기술

- TypeScript
- React
- Vite
- custom reactive state `@tldraw/state`
- typed record store
- schema/migration
- IndexedDB local persistence
- WebSocket sync
- SQLite sync storage adapter
- Cloudflare Durable Object/R2 계열 deployment 예제가 공식 제공됨

## Canonical data

```text
TLStore
├─ document scope
├─ session scope
└─ presence scope
```

## 입력 흐름

```text
Pointer/Keyboard
  ↓ InputsManager
StateNode
  ↓
Editor command
  ↓
Store mutation
  ↓
Reactive derivations
  ↓
Canvas render
```

## local persistence

```text
document records
+ local assets
    ↓
IndexedDB
```

## multiplayer

```text
Client Store
 ↓
TLSyncClient
 ↓ WebSocket
TLSocketRoom
 ↓
TLSyncRoom
 ↓
TLSyncStorage
```

server room이 authoritative document를 가진다.

## Asset

```text
TLAsset record = metadata/reference
TLAssetStore   = upload/resolve/remove
Blob storage   = IndexedDB / object storage 등
```

## migration

record migration + store migration을 지원한다.

sync 환경에서는 old/new client schema compatibility까지 검사한다.

## 주요 실패 상태

- 인증/권한
- client/server version
- invalid record
- rate limit
- room full
- message too large
- asset/storage failure

## 핵심 기술 판단

tldraw의 가장 강한 부분은 React가 아니라:

```text
State lifecycle
+ Typed Store
+ Migration
+ Editor command boundary
+ Sync protocol
```

의 결합이다.
