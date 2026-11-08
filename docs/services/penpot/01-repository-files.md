# Penpot — 저장소·폴더·파일 구조

공식 저장소: `penpot/penpot`  
기준 branch: `develop`

## 상위 구조

```text
penpot/
├─ frontend/
├─ backend/
├─ common/
├─ exporter/
├─ media-processor/
├─ render-wasm/
├─ plugins/
├─ mcp/
├─ library/
├─ docs/
├─ docker/
└─ operations/
```

## Frontend
ClojureScript + React.

```text
frontend/src/app/main/
├─ router.cljs
├─ store.cljs
├─ streams.cljs
├─ worker.cljs
├─ data/
├─ features/
└─ ui/
   ├─ auth/
   ├─ dashboard/
   ├─ workspace/
   ├─ viewer/
   ├─ settings/
   ├─ inspect/
   ├─ shapes/
   ├─ flex_controls/
   ├─ comments.cljs
   ├─ notifications/
   └─ routes.cljs
```

## Backend
Clojure/JVM.

공식 architecture:
- PostgreSQL
- Redis pub/sub
- RPC API
- WebSocket

## Common
front/back에서 공유하는 data structure, migration, component synchronization 등.

특히:
```text
common/src/app/common/files/migrations.cljc
common/src/app/common/types/component.cljc
```

## Exporter
ClojureScript/Node + headless Chrome/Puppeteer.

화면과 export의 rendering 차이를 줄이기 위해 별도 exporter service가 실제 browser rendering path를 이용한다.

## UI options 구조
공식 Data Guide:

```text
workspace/sidebar/options/
├─ shapes/
│  ├─ rect
│  ├─ circle
│  ├─ image
│  ├─ text
│  ├─ group
│  └─ multiple
├─ menus/
└─ rows/
```

- rows = 작은 input primitive
- menus = 속성 묶음
- shapes = type별 option composition

## Render / Import / Export

```text
ui/shapes/
worker/import/parser.cljs
ui/shapes/export.cljs
util/code_gen.cljs
util/markup_html.cljs
util/markup_svg.cljs
util/style_css.cljs
```

## Test
frontend/common/backend/exporter 각각 test tree를 보유한다.

이 구조는 Penpot이 단순 canvas app이 아니라 **frontend + backend + export + shared domain**으로 나뉜 full-stack editor임을 보여준다.
