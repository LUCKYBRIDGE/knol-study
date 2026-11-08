# AFFiNE 심층 분석

검토일: 2026-11-08  
근거: 공식 `toeverything/AFFiNE` canary + 공식 docs/product description  
근거 수준: **A1/A2**

# 1. 핵심 문제

AFFiNE은 Notion식 문서와 Miro식 무한 캔버스를 별도 앱으로 두지 않는다.

공식 표현:

> Docs, canvas and tables are hyper-merged.

핵심 질문:

> 같은 콘텐츠 block을 문서, whiteboard, database-like view에서 재사용하려면 underlying data model을 어떻게 설계해야 하는가?

---

# 2. Product Boundary

현재 repository:

```text
AFFiNE
├─ blocksuite/
├─ packages/frontend/
├─ packages/backend/
├─ packages/common/
├─ tests/
├─ desktop/mobile integration
└─ self-host docker
```

AFFiNE README가 밝히는 핵심 upstream:

```text
BlockSuite
  collaborative editor

y-octo / Yjs
  CRDT

OctoBase
  local-first collaborative database

Electron
  desktop

React
  app UI
```

즉 AFFiNE 자체 product layer 아래에 **editor framework + CRDT/data engine**이 따로 존재한다.

---

# 3. 가장 중요한 구조: Workspace

실제 source:
`packages/frontend/core/src/modules/workspace/entities/workspace.ts`

Workspace는 단순 folder가 아니라:
- docs
- blobs/assets
- sync
- workspace metadata
- server/cloud state
의 상위 context다.

논리 구조:

```text
Workspace
├─ Docs
│  ├─ Block tree
│  └─ Edgeless representation
├─ Metadata
├─ Blob storage
├─ Sync
├─ History
├─ Comments
└─ Permissions
```

---

# 4. BlockSuite가 editor core 역할

repository 안에:

```text
blocksuite/
├─ framework/
├─ affine/
├─ integration-test/
├─ playground/
└─ docs/
```

실제 API 문서에는 `Store`를 block lifecycle을 관리하는 core store로 설명한다.

즉 product UI가 직접 rich-text/canvas 데이터를 임의로 조작하는 대신 **BlockSuite Store** 위에서 document를 편집한다.

---

# 5. Doc과 Edgeless를 별도 파일이 아니라 같은 콘텐츠 세계로 본다

AFFiNE의 가장 독특한 부분.

일반적 서비스:

```text
문서 파일 A
화이트보드 파일 B
```

AFFiNE:

```text
Doc content
  ↕
Edgeless canvas representation
```

rich text, sticky note, linked page, database, shape, slide 등을 같은 workspace/block 기반으로 다룬다.

### 설계 의미

view와 canonical data를 가능한 한 분리하면:
- 같은 지식 객체를 여러 방식으로 표현
- 문서에서 canvas로 전환
- AI가 같은 data를 다양한 output으로 재구성
하기 쉬워진다.

---

# 6. Local-first가 product identity

공식 docs:

Local Workspace:
- data is stored locally
- CRDT 기반
- realtime sync/collaboration 가능

Pro/Cloud Workspace:
- cloud backup
- device sync
- history
- permission
- comments/notification

즉 cloud가 canonical source라는 전통 구조보다:

```text
Local data
  +
CRDT
  +
optional cloud sync/backup
```

방향이다.

---

# 7. CRDT layer

README가 명시하는 기반:

- Yjs
- y-octo
- OctoBase

목적:
- local ownership
- concurrent edits
- realtime collaboration
- client/server sync

이것은 tldraw의 server-authoritative room과 비교 분석 가치가 매우 크다.

```text
tldraw
server authoritative + rebase

AFFiNE
local-first CRDT
```

---

# 8. Blob와 document data 분리

실제 Workspace source에서 blob sync/source 관련 계층이 따로 존재한다.

논리적으로:

```text
CRDT document state
        ≠
Binary blobs
```

이미지/파일은 별도 blob lifecycle을 갖는다.

이는 tldraw/Excalidraw에서도 반복되는 패턴이다.

---

# 9. Cloud는 단순 sync 이상

공식 Workspace docs에 따르면 cloud/pro는:
- backup/sync
- unified page history
- permission
- comments + notifications
- templates
등을 추가한다.

즉:

```text
Local-first Core
       ↓ optional service
Cloud Collaboration Layer
       ↓
History / Permission / Comment / Backup
```

이다.

### 중요한 설계 교훈

local-first product에서도 cloud가 필요 없다는 뜻이 아니다.

Cloud는 **canonical content owner**가 아니라:
- coordination
- backup
- permission
- team service
를 제공할 수 있다.

---

# 10. Permission 구조

실제 backend code에는:
`packages/backend/server/src/core/permission/types.ts`

GraphQL schema에는:
- Doc_Update
- Doc_Users_Manage
- Doc_Users_Read
- Doc_Comments_Create
- Doc_Comments_Moderate
- Doc_Comments_Read
- Doc_History_Read
등 세밀한 permission이 존재한다.

즉 단순:
```text
viewer / editor
```
두 단계가 아니라 **capability permission** 방식으로 확장된다.

---

# 11. 테스트 구조

root에는:
- affine-cloud
- affine-cloud-copilot
- affine-desktop
- affine-desktop-cloud
- affine-local
- affine-mobile
- blocksuite
- fixtures

같은 test groups가 존재한다.

이는 local/cloud/desktop/mobile 조합이 실제 product complexity라는 것을 보여준다.

---

# 12. 문제를 풀어낸 방식

## 문서 ↔ 캔버스
같은 block/workspace model.

## Offline ↔ Cloud
local-first core + optional sync.

## 동시 편집
CRDT.

## Binary
blob store 분리.

## 개인 ↔ 팀
workspace + cloud permission.

## 다양한 client
web/desktop/mobile + shared core.

---

# 13. 장점

1. local-first를 처음부터 architecture 중심에 둔다.
2. doc/canvas/database를 하나의 block model로 통합한다.
3. CRDT로 offline/concurrent editing을 자연스럽게 연결한다.
4. cloud가 backup/permission/history layer로 명확하다.
5. BlockSuite/editor와 AFFiNE product layer가 분리된다.
6. binary/document 분리를 유지한다.

---

# 14. Trade-off

1. CRDT가 data model과 debugging을 어렵게 만든다.
2. local/cloud 상태 reconciliation과 blob sync가 복잡하다.
3. 모든 것을 block으로 통합하면 domain-specific UX가 약해질 위험이 있다.
4. web/desktop/mobile 모두 지원하면 lifecycle edge case가 크게 증가한다.
5. cloud permission과 local ownership 사이 정책이 복잡해진다.

---

# 15. Knol에 가져올 패턴

### 장기적으로 유용
```text
Local-first canonical document
+
optional cloud coordination
```

### 특히 참고
- 놀스토리 local project
- 놀아트 작업 복구
- 향후 사용자 기기간 sync
- 교사/학생 workspace
- binary blob separate sync

### 아직 가져오지 말 것
CRDT 자체를 현재 모든 Knol 서비스에 넣는 것.

실제 multi-device/realtime 요구가 생긴 서비스에만 적용해야 한다.

---

# 16. 추가 분석 과제

- BlockSuite Store/Schema 구체 타입
- page ↔ edgeless data relation
- Yjs/y-octo update flow
- OctoBase persistence
- blob sync
- cloud sync transport
- history snapshot
- comment anchor
- workspace/doc permission model
- desktop local DB
- mobile architecture
- migration

---

# 17. Source Evidence

- `toeverything/AFFiNE/README.md`
- `packages/frontend/core/src/modules/workspace/*`
- `blocksuite/framework/store/src/model/store/store.ts`
- `packages/backend/server/src/core/permission/types.ts`
- `packages/common/graphql/src/graphql/doc-role-permissions.gql`
- root `tests/`
- official AFFiNE Workspace documentation
