# 구조 분석 완성도와 미확인 영역

"완벽한 구조도"를 만들 때 가장 중요한 것은 **모르는 영역을 아는 척 채우지 않는 것**이다.

## tldraw

### 매우 높은 확신
- package topology
- state/store/schema/editor/sync boundaries
- record scopes
- migrations
- asset store abstraction
- sync client/room/storage architecture
- error reasons
- hosted app source tree

### 아직 완전하지 않은 부분
- tldraw.com production infrastructure의 실제 배포 토폴로지
- 내부 운영 DB/analytics retention
- 비공개 서비스 운영 정책

### 분석 완성도
**SDK/core: 매우 높음**  
**hosted production operations: 중간**

---

## Excalidraw

### 매우 높은 확신
- editor/element/scene app package boundaries
- AppState storage policy
- durable/ephemeral Store increments
- delta/history
- localStorage + IDB local persistence
- socket + Firebase hosted collaboration
- encrypted scene persistence

### 미확인/별도 영역
- 운영 중인 실제 production socket backend topology
- infrastructure scaling
- hosted deployment secrets/operations

### 분석 완성도
**client/editor architecture: 매우 높음**  
**production infra: 중간**

---

## H5P

### 매우 높은 확신
- framework interface
- file storage interface
- core validator/storage/export classes
- editor widget system
- content library manifest/semantics
- dependency categories
- package structure
- upgrade model

### 구조적으로 어려운 점
H5P는 한 제품 저장소가 아니라 생태계다.
WordPress/Drupal/Moodle/H5P.com 등 host implementation에 따라 실제 DB/cache/auth 구조가 달라진다.

### 분석 완성도
**H5P core protocol/ecosystem: 매우 높음**  
**특정 host deployment: 별도 분석 필요**

---

## Twine

### 매우 높은 확신
- app routes
- story/passages data model
- reducer/store
- undo
- persistence adapters
- publish binding
- story format extension system
- product design goals

### 변동 영역
- 실제 story runtime은 format(Harlowe/SugarCube 등)별로 다름
- Twine 자체 구조만 분석하면 published story runtime 전체를 설명할 수 없음

### 분석 완성도
**Twine authoring app: 매우 높음**  
**각 story format runtime: 별도 대상**

---

## Artsteps / Metasteps

### 확인 가능
- product capabilities
- creation flow
- artifacts
- guide/narrative
- share/embed
- avatar/social
- Metasteps portals/info points/guided paths/webviews/RBAC/districts

### 확인 불가
- source tree
- frontend framework
- rendering engine
- scene schema
- save protocol
- backend
- database
- object storage
- collaboration protocol
- deployment topology

### 분석 완성도
**product architecture: 중간~높음**  
**technical implementation architecture: 낮음/Unknown**

---

# 다음 분석을 더 깊게 할 수 있는 방법

## 공개 OSS
1. package.json dependency graph
2. code owners / test topology
3. event/action graph
4. serialization schema
5. migration history
6. network protocol message types
7. error taxonomy
8. performance/cache strategy
9. security/authorization boundaries
10. E2E scenario tests

## 비공개 SaaS
1. 공식 documentation
2. browser DevTools network observation
3. storage inspection
4. URL/share model
5. public API
6. import/export file inspection
7. accessibility tree
8. interaction recording
9. failure/offline behavior
10. terms/privacy/licensing

단, 인증 우회나 비공개 API 침해 같은 방식은 사용하지 않는다.
