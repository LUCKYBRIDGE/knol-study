# Penpot 심층 분석

검토일: 2026-11-08  
근거: 공식 `penpot/penpot` develop + 공식 technical guide  
근거 수준: **A1/A2**

## 1. 왜 분석 가치가 큰가

Penpot은 단순 Figma 대체제가 아니다.

핵심 설계 방향은:

- open-source / self-host
- SVG/CSS/HTML/JSON 같은 웹 표준
- real-time collaboration
- Components / Variants
- native Design Tokens
- CSS Grid / Flex Layout
- Inspect/code generation
- plugin / API / MCP

을 **하나의 설계 데이터 모델** 위에서 연결하는 것이다.

가장 중요한 질문은:

> 시각적 디자인 도구의 내부 객체를 실제 웹 구조와 얼마나 가깝게 만들 수 있는가?

---

# 2. 공식 전체 아키텍처

공식 technical guide:

```text
User
 ↓ HTTPS
Frontend App
  React + ClojureScript
  ↕ Web Worker
 ↓ RPC API
 ↓ WebSocket
Backend App
  Clojure/JVM
 ├─ PostgreSQL
 └─ Redis pub/sub

Frontend
 ↓ HTTPS
Exporter
  ClojureScript/Node
 ↓ Puppeteer
Headless Chrome
 ↓
Frontend/viewer rendering
```

## 핵심 구성

```text
frontend/
backend/
common/
exporter/
media-processor/
render-wasm/
plugins/
mcp/
library/
```

### frontend
workspace/editor/viewer/UI.

### backend
계정, 파일, 권한, persistence, collaboration backend.

### common
front/back가 공유하는 데이터 구조와 logic.

Clojure/ClojureScript를 함께 사용하는 이유 중 하나는 **동일한 data structure/code를 front/back에서 공유하기 쉽기 때문**이라고 공식 architecture 문서가 설명한다.

### exporter
별도 process.

Headless browser를 이용해 실제 rendering path를 활용한다.

### media-processor
media processing responsibility 분리.

### render-wasm
고성능 rendering 관련 별도 계층.

### plugins / mcp
automation 및 외부 extension boundary.

---

# 3. 데이터 무결성을 매우 중요하게 본다

공식 Data Guide 첫 문장부터 data structures를 Penpot의 가장 복잡하고 중요한 영역 중 하나로 설명한다.

특히 다음 경로를 모두 함께 고려해야 한다.

```text
Editor
 ↓
Database
 ↓
Load
 ↓
Migration
 ↓
Render
 ↓
Export
 ↓
Import
```

어느 한 경로에서 attribute를 빠뜨리면 데이터가 소실될 수 있다.

---

# 4. Shape attribute 설계 원칙

Penpot은 가능하면 shape/page attribute를 optional로 두는 방향을 사용한다.

예:

```text
fill-color 없음
→ 기본적으로 fill 없음

fill-color = null
→ 별도 상태로 기대하지 않음
```

import/export 과정에서 null attribute가 제거될 수 있으므로:

**null과 attribute absence를 다른 의미로 사용하지 않는 것**이 중요한 rule이다.

### 재사용 가치

JSON document를 장기 유지하는 앱에서도:

```text
missing = default
```

전략은 migration 비용을 크게 줄일 수 있다.

---

# 5. Migration

공식 경로:

```text
common/src/app/common/files/migrations.cljc
```

breaking model change:
1. file version 증가
2. migration 정의
3. old file load
4. 필요한 migration 순차 실행
5. 다음 저장에서 new version으로 저장

중요한 점은 **기존 데이터가 자동으로 새 feature를 가져야 하지 않도록 default behavior를 설계하는 것**이다.

---

# 6. Shape 편집 UI를 type별로 분리

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

### shapes
shape type별 editor composition.

### menus
속성 그룹 단위 reusable UI.

### rows
color input 등 작은 primitive.

즉:

```text
Primitive Row
   ↓
Property Menu
   ↓
Shape-specific Options
```

으로 구성한다.

---

# 7. Multiple Selection 문제를 명시적으로 해결

여러 다른 type shape를 동시에 선택했을 때:

- 공통 편집 가능한 attribute 계산
- 값이 동일하면 그 값 표시
- 서로 다르면 `:multiple`
- 해당 attribute를 지원하는 shape만 변경

예:

```text
Text + Rectangle 선택

Fill
  → 둘 다 가능

Font
  → Text만 가능

Border radius
  → Rect만 가능
```

이것은 복잡한 editor에서 매우 중요한 UX pattern이다.

---

# 8. Component synchronization — 원본과 Instance 관계

Penpot component copy는 main component 변경을 따라간다.

그러나 copy에서 사용자가 특정 속성을 수정하면 해당 sync group이 **touched** 상태가 된다.

```text
Main Component
  ↓ update fill

Instance A
  untouched
  → update 전달

Instance B
  fill touched
  → 해당 group update 차단
```

## 왜 좋은가

단순:
```text
원본이 바뀌면 전부 overwrite
```
가 아니다.

**상속 + local override** 모델이다.

이 패턴은 template 기반 콘텐츠, 복사본, 교사용 원본/학생 사본 구조에도 응용 가능하다.

---

# 9. Export와 Rendering을 가급적 같은 path로 사용

공식 Data Guide에서 중요한 부분.

Penpot SVG export는 workspace/viewer에서 shape를 rendering하는 체계를 재활용한다.

```text
Shape Data
  ↓
Shape Renderer
  ↓
SVG Node
  ├─ 화면 표시
  └─ Export
```

단, Penpot 내부 속성 중 SVG에 직접 표현할 수 없는 정보:
- proportion lock
- constraints
- stroke alignment
등은 metadata로 넣는다.

Import에서는:
```text
SVG + Penpot metadata
  ↓ parser
Penpot Shape
```

## 중요한 원리

**화면 renderer와 export renderer를 따로 구현하면 결과 불일치가 생기기 쉽다.**

---

# 10. Inspect / Design-to-code

Info tab:
- shape attributes 추출

Code tab:
- HTML
- CSS
- SVG
code generation

공식 source에는:
- code_gen
- markup_html
- markup_svg
- style_css
등이 분리되어 있다.

Penpot이 CSS Grid/Flex와 web standards를 강조하는 이유는 단순 marketing이 아니라 **design representation과 code representation 사이 거리를 줄이려는 구조적 선택**이다.

---

# 11. Collaboration architecture

공식 architecture에는:
- frontend ↔ backend WebSocket
- backend ↔ Redis pub/sub
가 명시된다.

즉 다중 backend process에서도 Redis가 realtime event distribution/broker 역할을 한다.

정확한 conflict algorithm은 별도 source 분석이 더 필요하다.

---

# 12. Exporter를 별도 service로 둔 이유

복잡한 design export는:
- frontend code
- SVG renderer
- font
- browser rendering
에 의존할 수 있다.

Penpot은 exporter + headless Chrome으로 실제 browser rendering을 활용한다.

### 장점
화면과 export 결과를 맞추기 쉬움.

### 비용
export infrastructure가 무거워짐.

---

# 13. UX로 복잡성을 풀어낸 방식

## Design primitive를 웹 primitive에 가깝게
Flex/Grid를 design-specific auto-layout abstraction 대신 실제 웹 개념과 가깝게 제공.

## Multiple selection
혼합 상태를 명시적으로 표현.

## Component instance
원본 업데이트를 받되 override 가능.

## Inspect
개발자가 별도 변환 과정 없이 code/spec을 볼 수 있게 함.

---

# 14. 장점

1. 데이터와 웹 표준 거리가 짧다.
2. open format/self-host 방향이 강하다.
3. component override 구조가 체계적이다.
4. migration/import/export를 data-model 설계에 포함한다.
5. 실제 web layout 개념을 designer에게 제공한다.
6. frontend/backend/common/exporter 책임이 비교적 명확하다.
7. plugin/API/MCP 확장 가능성이 크다.

---

# 15. Trade-off

1. Clojure/ClojureScript stack은 일반 JS 개발자에게 학습 비용이 있다.
2. SVG/web-standard 친화성은 자유로운 graphics engine 최적화와 trade-off가 있을 수 있다.
3. component synchronization/touched state는 구현과 사용자 mental model 모두 복잡하다.
4. exporter/headless browser는 운영 비용이 있다.
5. 디자인 시스템 기능이 많아 초보자 UI는 별도 단계화가 필요하다.

---

# 16. Knol에 가져올 패턴

## 매우 유용

### Main → Copy + touched override
놀스토리/놀아트 template 사본 구조.

### Render once, export same path
놀아트 export 정확도 개선.

### Multiple selection mixed state
다중 객체 편집.

### Optional attribute = default
장기 document schema.

### Layout semantics
CSS-like layout이 필요한 향후 제작도구.

---

# 17. 추가 심층 분석 과제

- 실제 file/page/object domain model
- design token data model
- component/variant internal records
- WebSocket event model
- Redis collaboration flow
- plugin sandbox/API
- media processor
- render-wasm
- test topology
- permission model

---

# 18. Source Evidence

공식:
- `penpot/penpot/README.md`
- `docs/technical-guide/developer/architecture/index.md`
- `docs/technical-guide/developer/data-guide.md`
- `docs/technical-guide/developer/frontend.md`
- repository root topology

공식 사이트:
- Penpot product page
- Technical Guide
