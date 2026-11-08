# 다음 분석 대상 로드맵

## 진행 상태

### 1차 분석 시작/완료
- ✅ Penpot — 1차 심층 case 작성
- ✅ AFFiNE — 1차 심층 case 작성
- ✅ MakeCode / PXT — 1차 심층 case 작성
- ✅ Padlet — 1차 제품 포렌식 case 작성

### 다음 순서
- ⏭ Scratch
- ⏭ Node-RED
- ⏭ Figma
- ⏭ n8n
- ⏭ Miro
- ⏭ Book Creator
- ⏭ Canva
- ⏭ Genially

---

검토일: 2026-11-08

현재 깊게 분석한 대상:
- tldraw
- Excalidraw
- H5P
- Twine
- Artsteps / Metasteps

다음 대상은 **기존 분석과 다른 구조적 문제를 잘 푼 제품**을 우선한다.

---

# 1차 분석군 — 가장 우선

## 1. Penpot

### 분석 가치
오픈소스 디자인 플랫폼이면서:
- responsive design
- CSS Grid / Flex layout
- component
- design token
- shared library
- prototype
- collaboration
- plugin / AI integration
을 한 제품 안에서 다룬다.

### 파헤칠 구조
- file/page/layer/object model
- component/instance
- design token
- shared library
- layout engine
- prototype graph
- collaboration
- persistence/backend
- plugin system
- self-host architecture
- import/export/open file format
- design-to-code boundary

### Knol에 유용
놀아트의:
- object/component 구조
- 편집 UI
- layout
- reusable asset
- design token

### 분석 방식
**A1 source-deep 가능.**

공식 repo:
`penpot/penpot`

---

## 2. AFFiNE

### 분석 가치
Docs + Whiteboard + Database를 한 workspace에서 결합하며 local-first를 핵심으로 한다.

공식 설명상:
- local workspace
- CRDT 기반 sync/collaboration
- cloud backup/sync
- page history
- permissions
- comments
- self-host

구조가 매우 흥미롭다.

### 파헤칠 구조
- Workspace
- Page
- Block
- Edgeless canvas
- Database/Table/Kanban
- local persistence
- CRDT/Octobase
- sync protocol
- cloud workspace
- history
- comments
- permission
- self-host backend

### Knol에 유용
특히:
- local-first architecture
- offline → cloud 확장
- document + canvas 통합
- version/history
- browser/device sync

### 분석 방식
**A1 source-deep 가능.**

공식 repo:
`toeverything/AFFiNE`

---

## 3. Microsoft MakeCode / PXT

### 분석 가치
초등·중등 교육용 도구 설계 관점에서 특히 중요.

하나의 프로젝트를:
- Blocks
- JavaScript
- Python 일부 target
- Simulator
- Hardware compile
로 연결한다.

### 파헤칠 구조
- PXT framework
- Target
- pxtarget.json
- Project
- Blocks AST
- TypeScript source
- blocks ↔ source conversion
- simulator
- extension/package
- compile
- UF2
- offline
- tutorial engine
- localization
- hardware abstraction

### 가장 중요한 질문
**블록과 텍스트 코드를 어떻게 같은 학습 progression 안에서 연결하는가?**

### Knol에 유용
- 놀퀴즈 교육용 activity
- 피지컬 코딩 서비스
- 단계형 학습
- 시뮬레이터 설계
- 확장 package

### 분석 방식
**A1/A2 source-deep 가능.**

공식 repos:
- `microsoft/pxt`
- `microsoft/pxt-blockly`
- `microsoft/pxt-common-packages`
- target repos

---

## 4. Scratch

### 분석 가치
초보자용 창작 프로그래밍의 대표 사례.

단순 block editor보다:
- sprite
- stage
- costume
- sound
- runtime
- event
- extension
- project persistence
- community/share
를 하나의 교육용 제작 시스템으로 묶는다.

### 파헤칠 구조
- Scratch Editor
- Blockly/block workspace
- VM
- runtime
- scheduler
- sprite/target
- variable/list
- event/broadcast
- renderer
- audio
- asset
- extension
- project format
- save/load
- remix/share boundary
- accessibility

### 중요
과거 `scratch-gui`, `scratch-vm` 일부가 archived 상태이므로 최신 구조는 **`scratchfoundation/scratch-editor` 중심으로 다시 추적**해야 한다.

### Knol에 유용
- 초등학생용 복잡 기능 단계화
- 즉시 실행 feedback
- 창작물 모델
- 이벤트 기반 runtime
- 교육용 extension

### 분석 방식
**A1 source-deep 가능.**

---

## 5. Node-RED

### 분석 가치
"그래프를 그리는 UI"와 "실제로 실행되는 flow runtime"을 매우 명확히 연결한다.

### 파헤칠 구조
- Flow
- Node
- Wire
- Message
- Runtime
- Editor
- Palette
- Node registry
- Deploy
- Context
- Subflow
- Credentials
- import/export JSON
- custom node
- runtime/editor separation

### 중요한 질문
**시각적 그래프를 실제 실행 시스템으로 어떻게 변환하는가?**

### Knol에 유용
- 분기/시나리오
- 교육 활동 workflow
- 관리자 자동화
- 향후 시각적 logic builder

### 분석 방식
**A1/A2 source-deep 가능.**

공식 repo:
`node-red/node-red`

---

## 6. n8n

### 분석 가치
Node-RED보다 업무 workflow/automation 운영 측면이 강하다.

### 파헤칠 구조
- workflow graph
- node type
- trigger/action
- execution
- partial execution
- credentials
- retry
- queue
- webhook
- expression
- binary data
- execution history
- sub-workflow
- permission/project
- worker/scaling
- node package ecosystem
- AI node/tool integration

### Node-RED와 비교할 핵심
```text
Node-RED
  flow runtime / edge / event-driven

n8n
  automation job / execution persistence / credentials / operations
```

### Knol에 유용
직접 학생용 UI보다:
- 관리자 workflow
- AI agent/tool orchestration
- 자동 작업 시스템
설계 참고 가치가 큼.

### 분석 방식
**source-deep 가능하나 fair-code/license 경계 별도 확인 필요.**

공식 repo:
`n8n-io/n8n`

---

## 7. Padlet

### 분석 가치
사용자가 실제 수업에서 자주 접하는 구조이고, **교육용 협업 UX**를 잘 연구할 수 있다.

공식 제품에서:
- Board
- Sandbox
- rich media post
- realtime collaboration
- comments/reactions
- activity log
- 여러 layout
- password/invite/team/school permission
- link/QR/embed
를 제공한다.

### 파헤칠 구조
공개 source가 없으므로 제품 포렌식 중심.

- Board vs Sandbox
- Post
- Attachment
- Comment
- Reaction
- layout abstraction
- realtime update
- activity feed
- permission
- anonymous/guest contribution
- moderation
- sharing
- presentation
- export
- classroom UX
- mobile UX

### 특히 분석할 질문

**같은 Post 데이터가 어떻게 여러 layout으로 표현되는가?**

예:
```text
Post collection
  ↓
Wall
Columns
Map
Timeline
Freeform
...
```

이 구조는 교육용 정보 정리 서비스에 매우 유용하다.

### Knol에 유용
- 놀아트 작품 공유
- 학급 공동 보드
- 작품 피드백
- 학생별/모둠별 제출
- 교사 moderation

### 분석 방식
**B/C product-forensics 중심.**

---

## 8. Figma

### 분석 가치
비공개 제품이지만 공개 API/개발문서가 매우 깊다.

공식 Plugin API에서 file이:
```text
DocumentNode
  ↓
PageNode
  ↓
Scene Nodes
```
tree로 표현된다는 점까지 확인 가능.

또:
- components/instances
- libraries
- variables
- variable modes
- Plugin API
- Widget API
- REST API
- Dev Mode
- Code Connect
가 각각 다른 경계를 갖는다.

### 파헤칠 구조
- document node tree
- component/instance
- main component update propagation
- style
- variable/collection/mode/alias
- team library
- plugin sandbox
- plugin current-file boundary
- Widget
- REST read boundary
- Dev Mode
- Code Connect
- design→code handoff
- permission/version history
- multiplayer UX

### Knol에 유용
- reusable component
- 원본 ↔ instance
- design token
- mode
- plugin boundary
- 제작자 ↔ 실행자/개발자 handoff

### 분석 방식
내부 server/source는 비공개이므로:
**A2/B + 공개 API 기반 구조 분석.**

---

# 2차 분석군 — 1차 이후

## 9. Miro

### 이유
Figma/tldraw와 다른 **업무 객체 중심 무한 캔버스**.

공개 Developer Platform에서:
- Board
- Board Item
- parent/child hierarchy
- Web SDK
- REST API
- Live Embed
- Marketplace App
구조가 드러난다.

### 분석 포인트
- board item hierarchy
- structured format
- widget/app
- integration
- external data card
- embed
- templates
- permissions
- facilitation
- TalkTrack
- AI workflow

---

## 10. Canva

### 이유
하나의 canvas editor보다 **Visual Suite** 관점이 중요.

현재 공식 제품은:
- Docs
- Sheets
- Whiteboards
- Presentations
- Websites
- Video
- Social
- Email
- Code
등을 하나의 제작 환경에서 연결한다.

### 분석 포인트
- cross-format design
- template-first onboarding
- brand system
- asset library
- resize/reformat
- publish/export
- collaboration
- Apps ecosystem
- AI action integration
- offline/sync UX

### Knol에 유용
여러 독립 서비스를 하나의 Knol suite로 연결할 때 참고.

---

## 11. Book Creator

### 이유
학생용 multimodal creation과 교사용 management를 연결한다.

### 분석 포인트
- Book
- Page
- media object
- read mode
- publish
- classroom/library
- Class View
- student submission/portfolio
- accessibility
- UDL
- teacher/student role

### 특히 유용
초등학생 제작 UI의 난이도와 접근성.

---

## 12. Genially

### 이유
Interactive presentation/gamification authoring 분석용.

### 분석 포인트
- page/scene
- object
- animation
- interaction
- hotspot
- overlay
- branching
- template
- gamification
- publication/embed

H5P와 비교하면 좋다.

```text
H5P
  structured educational content type

Genially
  free-form visual interactive authoring
```

---

# 3차 비교·보완 후보

## AppFlowy
AFFiNE와 비교하여:
- local workspace
- database
- collaboration cloud
- self-host
- AI
를 다른 architecture로 어떻게 풀었는지 비교.

## Obsidian
- local Markdown filesystem
- vault
- link graph
- metadata
- plugin
- workspace
를 분석할 가치가 큼.

## Notion
- block model
- database/view
- relation/rollup
- synced content
- permission
- template
를 product-level로 분석.

## Kunstmatrix / Spatial / 유사 3D 플랫폼
Artsteps와 다른 3D gallery/social-space 해법 비교.

## GitBook / Outline
문서 publishing, version, collection, permission 구조 분석.

---

# 분석 순서 제안

## Batch A — 제작·편집기 구조

1. Penpot
2. Figma
3. Miro
4. Canva

질문:
**복잡한 제작 기능을 어떻게 객체·컴포넌트·UI로 조직하는가?**

## Batch B — local-first / 문서

1. AFFiNE
2. AppFlowy
3. Obsidian

질문:
**로컬 데이터와 cloud sync를 어떻게 공존시키는가?**

## Batch C — 교육용 authoring

1. MakeCode
2. Scratch
3. Padlet
4. Book Creator
5. Genially

질문:
**어린 학습자와 교사가 복잡한 기능을 어떻게 배우고 공유하는가?**

## Batch D — workflow/runtime

1. Node-RED
2. n8n

질문:
**시각적 노드 그래프가 실제 실행·오류·재시도 시스템으로 어떻게 연결되는가?**

---

# 현재 추천 우선순위

| 순위 | 제품 | 새로 확보할 핵심 패턴 | 소스 깊이 |
|---:|---|---|---|
| 1 | Penpot | design system + layout + open editor | 매우 높음 |
| 2 | AFFiNE | local-first + CRDT + doc/canvas/database | 매우 높음 |
| 3 | MakeCode | blocks↔code + simulator + target/extensions | 매우 높음 |
| 4 | Scratch | beginner creator + event runtime + project model | 매우 높음 |
| 5 | Node-RED | visual graph → runtime | 매우 높음 |
| 6 | Padlet | classroom collaboration + multi-layout + permission | 제품 분석 |
| 7 | Figma | component/instance + variable mode + plugin boundary | API/공식문서 높음 |
| 8 | n8n | execution engine + credential + retry/operations | 높음 |
| 9 | Miro | board object + app/integration ecosystem | API/공식문서 높음 |
| 10 | Book Creator | student authoring + class management + accessibility | 제품 분석 |
| 11 | Canva | multi-format suite + template/brand/AI | 제품 분석 |
| 12 | Genially | interaction/gamification authoring | 제품 분석 |

---

# 분석 문서 산출 기준

각 제품은 기존 템플릿에 따라 최소 다음 4개 축을 남긴다.

```text
structure/
  전체 구조도

forensics/
  문제를 풀어낸 방식

patterns/
  재사용 가능한 메커니즘

fit-gap/
  Knol 서비스 적용 여부
```

공개 source가 있는 제품은:
- repository tree
- package dependency
- 핵심 type
- persistence
- sync
- test
까지 내려간다.

비공개 SaaS는:
- 공식 API
- product flow
- data/export model
- permission
- browser behavior
를 확인하고 내부 DB/server는 Unknown으로 남긴다.
