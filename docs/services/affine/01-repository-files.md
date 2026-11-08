# AFFiNE — 저장소·폴더·파일 구조

공식 저장소: `toeverything/AFFiNE`  
기준 branch: `canary`

## 상위

```text
AFFiNE/
├─ blocksuite/
│  ├─ affine/
│  ├─ framework/
│  ├─ integration-test/
│  └─ playground/
├─ packages/
│  ├─ frontend/
│  │  ├─ core/
│  │  ├─ apps/
│  │  ├─ component/
│  │  ├─ routes/
│  │  ├─ native/
│  │  └─ mobile-native/
│  ├─ backend/
│  │  ├─ server/
│  │  └─ native/
│  └─ common/
├─ tests/
│  ├─ affine-cloud/
│  ├─ affine-cloud-copilot/
│  ├─ affine-desktop/
│  ├─ affine-desktop-cloud/
│  ├─ affine-local/
│  ├─ affine-mobile/
│  └─ blocksuite/
├─ .docker/
└─ docs/
```

## BlockSuite

```text
blocksuite/framework/store/
├─ model/
├─ schema/
├─ reactive/
├─ yjs/
├─ transformer/
├─ adapter/
├─ extension/
└─ __tests__/
```

AFFiNE editor의 core block lifecycle은 여기에서 관리된다.

## AFFiNE-specific blocks/widgets

```text
blocksuite/affine/
├─ blocks/
├─ gfx/
│  ├─ shape
│  ├─ connector
│  ├─ mindmap
│  ├─ note
│  ├─ text
│  └─ turbo-renderer
├─ inlines/
├─ rich-text/
├─ widgets/
│  ├─ edgeless-toolbar
│  ├─ edgeless-zoom-toolbar
│  ├─ slash-menu
│  ├─ toolbar
│  ├─ remote-selection
│  └─ ...
├─ model/
└─ shared/
```

## Frontend Workspace

```text
core/src/desktop/pages/workspace/
├─ all-page/
├─ journals/
├─ collection/
├─ all-collection/
├─ tag/
├─ all-tag/
├─ attachment/
├─ chat/
├─ detail-page/
├─ settings/
├─ share/
└─ trash-page
```

## Backend
permission, GraphQL, sync/cloud, history, comment, member 기능이 분리되어 있다.

예:
`packages/backend/server/src/core/permission/types.ts`
