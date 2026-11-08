# 기능 → 내부 해결 방식 지도

기능 목록과 내부 메커니즘을 연결한 레퍼런스.

| 기능/역량 | tldraw | Excalidraw | H5P | Twine | Artsteps/Metasteps |
|---|---|---|---|---|---|
| 기본 제작 | Editor + tools + shapes | App/actions/elements | Editor + semantics | Passage editor | VR Editor |
| 객체 확장 | ShapeUtil | element/action source extension | content library | Story Format | 내부 개발 확장 Unknown |
| 관계 | Binding records | bound elements/arrows/groups | nested/dependency libraries | passage links/references | placement/path/portal |
| 다단계 입력 | StateNode | App feature handlers + capture | field/widget | direct text/map editing | 3D authoring interaction |
| 구조 시각화 | canvas | canvas | activity별 | Story Map | 3D space |
| 검색/탐색 | app-defined | search UI | Hub/content selector | fuzzy passage finder | hosted browse |
| undo | Editor history | delta history | editor-specific | undoable stories + text undo | Unknown |
| local autosave | IndexedDB persistence | localStorage + IDB | host-dependent | browser local storage | 제품상 autosave 관련 언급은 있으나 내부 Unknown |
| binary asset | TLAssetStore | BinaryFiles/FileManager | host file storage/package | published story asset 방식 다양 | uploaded artifact |
| import/export | snapshot/image | .excalidraw/PNG/SVG | .h5p | HTML/Twee/story import | share/embed 중심 |
| migration | StoreSchema/record migrations | restore/resave/version handling | upgrades.js/library version | repair/import/version handling | Unknown |
| realtime collab | sync-core | Socket + Firebase | core 핵심 아님 | core 핵심 아님 | social presence 확인, author collab Unknown |
| presence | presence records | collaborators/pointer | activity/host dependent | 없음(core authoring) | avatar/chat |
| encryption | deployment choice | room E2EE | host/security model | 없음(core) | Unknown |
| permission | server room hooks | hosted room/app | host + trusted library permission | local authoring 중심 | private/member/role |
| learner runtime | app-specific | editor 자체 | content runtime | Story Format runtime | visitor runtime |
| resume/progress | app-defined | local editor restoration | getCurrentState + host | story-format dependent | Unknown |
| accessibility | SDK support | editor accessibility | content-type별 | explicit design goal + axe tests | 공개 기술 수준 미확인 |
| mobile/touch | adaptive editor | mobile toolbar/gesture | content type dependent | explicit touch goal | mobile/tablet navigation 공식 안내 |
| publication | app-defined | share link/export | host publish/embed | standalone HTML | hosted publish/share/embed |
| multi-space | app-defined | canvas pages/frames | content type dependent | story graph | Metasteps Portal |
| guided route | app-defined | 없음 핵심 | Game Map 등 타입별 | story links | Guided Paths |

# 읽는 법

동일한 체크 표시가 같은 구현을 뜻하지 않는다.

예를 들어 "관계"는:
- tldraw = persistent record relation
- Twine = narrative graph
- H5P = module dependency/nested activity
- Artsteps = spatial route

다른 domain semantics를 갖는다.

# 우리 서비스에 기능을 추가할 때

기능 이름으로 레퍼런스를 고르지 말고 **내부 문제가 무엇인지**를 보고 선택한다.

예:
- "공유 기능"이 필요하다
  - 단순 링크 공유인가?
  - 파일 portability인가?
  - realtime collaboration인가?
  - public publication인가?
  - organization permission인가?

각각 전혀 다른 구조다.
