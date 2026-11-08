# 구조도 소스 근거 지도

검토 기준: 2026-11-08

이 문서는 구조도 안의 각 박스가 **어떤 공식 소스 파일에서 확인되는지** 추적하기 위한 인덱스다.

---

# tldraw

| 구조 요소 | 공식 소스 |
|---|---|
| reactive atom/computed/effect | `packages/state/src/lib/Atom.ts`, `Computed.ts`, `EffectScheduler.ts` |
| transaction/rollback | `packages/state/src/lib/transactions.ts` |
| typed store | `packages/store/src/lib/Store.ts` |
| record type/scope | `packages/store/src/lib/RecordType.ts` |
| store schema/migration | `packages/store/src/lib/StoreSchema.ts` |
| document record | `packages/tlschema/src/records/TLDocument.ts` |
| session state | `TLInstance.ts`, `TLCamera.ts`, `TLPageState.ts`, `TLPointer.ts` |
| presence | `packages/tlschema/src/records/TLPresence.ts` |
| schema composition | `packages/tlschema/src/createTLSchema.ts` |
| store factory | `packages/editor/src/lib/config/createTLStore.ts` |
| editor | `packages/editor/src/lib/editor/Editor.ts` 및 editor subtree |
| local IndexedDB | `packages/editor/src/lib/utils/sync/LocalIndexedDb.ts` |
| sync client | `packages/sync-core/src/lib/TLSyncClient.ts` |
| server room | `packages/sync-core/src/lib/TLSocketRoom.ts` |
| authoritative sync room | `packages/sync-core/src/lib/TLSyncRoom.ts` |
| persistence abstraction | `packages/sync-core/src/lib/TLSyncStorage.ts` |
| memory/sql storage | `InMemorySyncStorage.ts`, `SQLiteSyncStorage.ts` |
| asset abstraction | `packages/tlschema/src/TLStore.ts`의 `TLAssetStore` |
| hosted product | `apps/dotcom/` |

### 근거 수준
거의 전부 A1.

---

# Excalidraw

| 구조 요소 | 공식 소스 |
|---|---|
| editor component | `packages/excalidraw/index.tsx` |
| app state | `packages/excalidraw/appState.ts` |
| state destination policy | `APP_STATE_STORAGE_CONF` in `appState.ts` |
| element model/domain | `packages/element/src/*` |
| scene store | `packages/element/src/store.ts` |
| durable/ephemeral increment | `packages/element/src/store.ts` |
| delta model | `packages/element/src/delta.ts` |
| history | `packages/excalidraw/history.ts` |
| scene renderer | `packages/excalidraw/scene/Renderer.ts` |
| export | `packages/excalidraw/scene/export.ts` |
| import/restore | `packages/excalidraw/data/restore.ts`, `json.ts` |
| encryption | `packages/excalidraw/data/encryption.ts` |
| local persistence | `excalidraw-app/data/LocalData.ts` |
| binary file manager | `excalidraw-app/data/FileManager.ts` |
| browser tab sync | `excalidraw-app/data/tabSync.ts` |
| Firebase durable scene | `excalidraw-app/data/firebase.ts` |
| realtime collaboration | `excalidraw-app/collab/Portal.tsx`, `Collab.tsx` |
| hosted app | `excalidraw-app/` |

### 근거 수준
거의 전부 A1.

---

# H5P

| 구조 요소 | 공식 소스 |
|---|---|
| host platform contract | `h5p-php-library/h5p.classes.php` — `H5PFrameworkInterface` |
| file storage contract | `h5p-file-storage.interface.php` |
| core | `H5PCore` in `h5p.classes.php` |
| package validator | `H5PValidator` |
| content semantics validator | `H5PContentValidator` |
| save/install | `H5PStorage` |
| export | `H5PExport` |
| default filesystem storage | `h5p-default-storage.class.php` |
| runtime boot | `js/h5p.js`, `embed.php` |
| runtime event system | `js/h5p-event-dispatcher.js` |
| xAPI | `js/h5p-x-api.js`, `h5p-x-api-event.js` |
| content upgrade | `js/h5p-content-upgrade*.js` |
| generic editor core | `h5p-editor-php-library/h5peditor.class.php` |
| editor JS widgets | `h5p-editor-php-library/scripts/h5peditor-*.js` |
| content manifest | 각 library의 `library.json` |
| authoring/data semantics | 각 library의 `semantics.json` |
| library migration | 각 library의 `upgrades.js` |
| pre-save transform | 각 library의 `presave.js` |

### 근거 수준
A1 + 공식 specification A2.

---

# Twine

| 구조 요소 | 공식 소스 |
|---|---|
| story/passage types | `src/store/stories/stories.types.ts` |
| story reducers/actions | `src/store/stories/` |
| undo | `src/store/undoable-stories/` |
| persistence | `src/store/persistence/` |
| persistence filtering | `persistable-changes.ts` |
| browser persistence | `persistence/local-storage/` |
| desktop persistence | `persistence/electron-ipc/` |
| story format store | `src/store/story-formats/` |
| publishing binding | `src/store/use-publishing.ts` |
| publish compiler | `src/util/publish.ts` |
| link parsing | `src/util/parse-links.ts` |
| Story Map UI | `src/routes/story-edit/`, `src/components/story/`, `passage/` |
| search | `dialogs/story-search`, `components/fuzzy-finder` |
| extension contract | `EXTENDING.md` |
| product design principles | `DESIGN_GOALS.md` |

### 근거 수준
A1/A2.

---

# Artsteps / Metasteps

공개 source repository가 없어 제품 구조 근거는 공식 웹/블로그 기준.

| 구조 요소 | 공식 근거 |
|---|---|
| 3D exhibition creation | Artsteps About |
| image/photo/poster artifact | Artsteps About |
| 3D object artifact | Artsteps About |
| streaming video | Artsteps About |
| browser-based creation | Artsteps About |
| navigation on computer/mobile/tablet/VR | 2020 official article |
| avatar/chat/comment | 2020 official article |
| share/embed | Artsteps About/2020 article |
| Artsteps → Metasteps evolution | 2025-09-15 official post |
| Portal | 2025 Metasteps post |
| Info Point | 2025 Metasteps post |
| multiple Guided Paths | 2025 Metasteps post |
| Webviews | 2025 Metasteps post |
| member-only access / role permissions | 2025 Metasteps post |
| Districts branded hub | 2025 Metasteps post |

### 근거 수준
B. 내부 module/server/database 구조는 Unknown.

---

# 원칙

구조도에 새 박스를 추가할 때:
1. 먼저 이 근거 지도에 source를 추가한다.
2. source가 없으면 B/C/Unknown으로 표기한다.
3. 공개 source를 찾지 못했다는 이유로 익숙한 기술 스택을 임의로 대입하지 않는다.
