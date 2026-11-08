# AFFiNE — 기술·데이터 흐름

## 기술
- TypeScript / React
- BlockSuite
- Yjs
- y-octo
- OctoBase
- Rust native/data engine 계열
- GraphQL
- Electron
- mobile native integration
- Docker self-host
- CRDT

## 핵심 계층

```text
AFFiNE Product
  ↓
BlockSuite Editor
  ↓
Store / Schema / Yjs
  ↓
local-first database / OctoBase
  ↓ optional
Cloud sync/backend
```

## Workspace

```text
Workspace
├─ Docs
├─ Metadata
├─ Blob source
├─ Sync
├─ History
├─ Comments
└─ Permissions
```

## Local-first

```text
local canonical state
  ↕ CRDT updates
optional cloud
  → backup
  → multi-device sync
  → collaboration
  → history
  → permissions/comments
```

## Binary
document CRDT와 blob source/sync를 분리한다.

## Doc + Edgeless
같은 underlying block world를 여러 편집/view mode로 제공하는 방향.

## backend permission
capability 수준의 permission이 GraphQL/backend에서 노출된다.

## 테스트가 보여주는 복잡성
- local
- cloud
- desktop
- desktop+cloud
- mobile
- blocksuite
조합을 따로 테스트한다.
