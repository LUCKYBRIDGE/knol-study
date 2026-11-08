# tldraw — 저장소·폴더·파일 구조

공식 저장소: `tldraw/tldraw`  
기준 branch: `main`

## 1. Monorepo 상위 구조

```text
tldraw/
├─ apps/
│  ├─ dotcom/                 tldraw.com 실제 제품
│  ├─ docs/
│  ├─ examples/
│  ├─ vscode/
│  ├─ analytics/
│  ├─ analytics-worker/
│  └─ ...
├─ packages/
│  ├─ state/
│  ├─ state-react/
│  ├─ store/
│  ├─ tlschema/
│  ├─ editor/
│  ├─ tldraw/
│  ├─ sync-core/
│  ├─ sync/
│  ├─ collaboration/
│  ├─ assets/
│  ├─ commenting/
│  ├─ mentions/
│  ├─ collections/
│  └─ utils/
├─ templates/
└─ internal/
```

## 2. 실제 hosted client

```text
apps/dotcom/client/
├─ src/
│  ├─ main.tsx
│  ├─ routeDefs.ts
│  ├─ routes.tsx
│  ├─ pages/
│  ├─ tla/
│  │  ├─ pages/
│  │  ├─ providers/
│  │  └─ ...
│  ├─ components/
│  ├─ hooks/
│  └─ utils/
├─ e2e/
├─ public/
├─ scripts/
└─ styles/
```

특히 `routeDefs.ts`와 `routes.tsx`가 실제 서비스 페이지 경계를 보여준다.

## 3. UI package

```text
packages/tldraw/src/lib/ui/
├─ TldrawUi.tsx
├─ components/
│  ├─ MainMenu/
│  ├─ Toolbar/
│  ├─ StylePanel/
│  ├─ ActionsMenu/
│  ├─ ContextMenu/
│  ├─ HelpMenu/
│  ├─ NavigationPanel/
│  ├─ PageMenu/
│  ├─ SharePanel/
│  ├─ Minimap/
│  ├─ ZoomMenu/
│  ├─ QuickActions/
│  ├─ AccessibilityMenu.tsx
│  └─ MobileStylePanel.tsx
├─ context/
├─ hooks/
│  ├─ useTools.tsx
│  ├─ useExportAs.ts
│  ├─ useCopyAs.ts
│  ├─ usePrint.ts
│  └─ ...
└─ overrides.ts
```

## 4. Editor engine

```text
packages/editor/src/lib/editor/
├─ Editor.ts
├─ tools/
│  ├─ RootState.ts
│  ├─ StateNode.ts
│  └─ BaseBoxShapeTool/
├─ shapes/
│  ├─ ShapeUtil.ts
│  └─ ...
├─ bindings/
│  └─ BindingUtil.ts
├─ managers/
│  ├─ InputsManager
│  ├─ HistoryManager
│  ├─ SnapManager
│  ├─ SpatialIndexManager
│  ├─ CollaboratorsManager
│  ├─ PerformanceManager
│  ├─ TextManager
│  ├─ FontManager
│  ├─ ThemeManager
│  └─ UserPreferencesManager
├─ derivations/
├─ overlays/
└─ types/
```

## 5. Data / schema

```text
packages/store/
  Store.ts
  StoreSchema.ts
  RecordType.ts

packages/tlschema/src/records/
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
  TLUser
```

## 6. Sync

```text
packages/sync-core/src/lib/
├─ TLSyncClient.ts
├─ TLSocketRoom.ts
├─ TLSyncRoom.ts
├─ TLSyncStorage.ts
├─ InMemorySyncStorage.ts
├─ SQLiteSyncStorage.ts
├─ ClientWebSocketAdapter.ts
├─ ServerSocketAdapter.ts
├─ protocol.ts
├─ diff.ts
└─ recordDiff.ts
```

## 7. Test topology

```text
editor:
  managers / StateNode / geometry / LocalIndexedDb

sync-core:
  protocol / diff / storage
  TLSyncClientRebase
  TLSocketRoom
  syncFuzz
  upgradeDowngrade

dotcom:
  Playwright E2E
```

이 구조 자체가 tldraw가 중요하게 보는 경계를 보여준다.
