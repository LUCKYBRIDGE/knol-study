# 데이터·상태 생명주기 비교표

이 문서는 "무슨 데이터가 어디까지 살아남는가?"를 5개 도구에서 비교한다.

## 1. 핵심 비교

| 상태 종류 | tldraw | Excalidraw | H5P | Twine | Artsteps |
|---|---|---|---|---|---|
| canonical content | document records | elements + persisted scene state | content params | story/passages | exhibition scene 추론 |
| editor session | session records | AppState | editor form state | Story/Passage 일부 UI fields + prefs | authoring state Unknown |
| collaboration presence | presence records | socket collaborator/pointer state | host/content type dependent | core authoring에는 중앙 collab 없음 | avatar/social 존재, 구현 Unknown |
| binary assets | TLAssetStore | BinaryFiles/FileManager | host file storage/package media | story/output assets | uploaded artifacts |
| runtime progress | 별도 app이 정의 가능 | editor 자체가 runtime | content state/user data | Story Format runtime | viewer progress Unknown |
| export | store snapshot/image | .excalidraw/PNG/SVG | .h5p | HTML/Twee/archive | hosted share/embed 중심 |
| schema migration | StoreSchema migrations | restore/resave/version mechanisms | library upgrades | app/story repair/import logic | Unknown |

## 2. tldraw

```text
Document record
  local persistence: yes
  sync: yes
  snapshot: yes

Session record
  sync: no
  일부 세션 보존 가능

Presence
  sync: yes
  durable persistence: no
```

추가로 `TLInstance` field별 preserve 여부가 존재한다.

### 특징
**type-level lifecycle → field-level preservation**의 2단계.

---

## 3. Excalidraw

```text
Elements
  durable change / history / local / export / collab

AppState
  field별 browser/export/server policy

BinaryFiles
  local IndexedDB
  collaboration에서는 별도 remote file storage
```

변경 자체도:
- IMMEDIATELY
- NEVER
- EVENTUALLY
로 분류.

### 특징
**state destination + change durability**의 2차원 모델.

---

## 4. H5P

```text
Content params
  host DB
  export package

Library code
  installed library store

Assets
  host file store/package

Editor form state
  authoring transient

Runtime/user state
  host/content-type support에 따라 별도
```

### 특징
콘텐츠 인스턴스와 콘텐츠 타입 코드가 분리됨.

---

## 5. Twine

Twine은 비교적 느슨하다.

```text
Story/Passage object
  content field
  + selected/highlighted/zoom 같은 authoring UI field 일부 포함
        ↓
persistable-change filter
        ↓
browser/Electron persistence
```

즉 type 구조 자체보다 **change filter**에서 persistence 경계를 만든다.

### 특징
구조 단순성은 높지만 domain/session 경계는 tldraw보다 약함.

---

## 6. Artsteps

외부 관찰 가능 수준:

```text
Exhibition content
Space / artifacts / publication
  durable hosted content

Visitor/avatar/social state
  account/service state로 보이나 내부 저장 구조 Unknown

Navigation transient state
  Unknown
```

Metasteps의 role/member/district 기능으로 보아 content와 membership/permission domain은 분리되어 있을 가능성이 높지만 내부 구현은 확인 불가(C).

---

# 7. Knol에 가장 유용한 통합 모델

```text
CanonicalContent
EditorSession
RuntimeSession
Presence
Asset
Permission
```

각 field는 다시:

```text
browser?
export?
server?
realtime?
history?
migration?
```

정책을 가진다.

이 두 층을 함께 쓰면 가장 안정적이다.
