# 테스트·품질·회귀 방지 전략 비교

검토일: 2026-11-08

좋은 아키텍처는 코드 구조뿐 아니라 **무엇을 테스트하는가**에서 드러난다.

# 1. tldraw

## 관찰되는 테스트 계층

### Unit
- geometry primitives
- managers
- StateNode
- store
- schema
- migrations
- LocalIndexedDb

### Sync contract
- protocol
- diff
- recordDiff
- storage contract
- socket adapters
- SQLite wrappers

### Distributed behavior
- TLSyncClientRebase
- TLSyncRoom
- TLSocketRoom

### Fuzz
random operation + disconnect/reconnect + message flush 순서를 바꾸며 최종 수렴 확인.

### Compatibility
upgrade/downgrade tests.

### E2E
examples/dotcom Playwright 계층.

## 핵심 invariant

```text
모든 client의 document scope store가 결국 같아야 한다.
```

### 우리가 배울 점
동기화 기능에는 example test보다:
- random order
- reconnect
- migration
- stale client
가 중요하다.

---

# 2. Excalidraw

## 테스트 범위가 매우 넓음

- App interaction
- actions
- history
- restore
- reconcile
- export
- image
- arrow binding
- selection
- clipboard
- lasso
- move/rotate
- free draw
- viewport
- scrolling
- file drop
- library
- mobile menu
- collaboration
- Firebase
- render cache

## Collaboration invariant

reconcile test는:
- local/remote source choice
- version
- versionNonce
- order/index
- convergence
- duplicate index 방지
를 검증.

## 핵심 특징

대규모 interactive editor에서는 **사용자 interaction regression test 수가 많다.**

KnolArt처럼 UI와 geometry가 복잡해질수록 unit function test만으로 부족하다.

---

# 3. Twine

## 테스트 형태

- React component unit/integration
- store/reducer
- persistence
- passage connection
- dialog
- search
- route
- Electron main-process
- Playwright smoke E2E

## 접근성

많은 component test에서 `jest-axe`를 사용.

예:
- broken connection
- self connection

즉 visual graph element도 accessibility test 대상으로 본다.

## 사용자 구조 invariant

- broken link가 제대로 보이는가
- self-link가 정상적으로 보이는가
- start marker가 올바른가
- selected passage 이동 offset이 맞는가

### 배울 점
Story Map을 만들면 graph algorithm만 테스트하지 말고 **표시의 의미**도 테스트해야 한다.

---

# 4. H5P

검토한 core/editor/MultiChoice/InteractiveVideo repository에서는 대규모 test directory가 드러나지 않는다.

대신 architecture 자체가 다음 보호 장치를 가진다.

- .h5p package validation
- file whitelist
- file/total size
- h5p.json validation
- library.json validation
- semantics content validation
- dependency check
- library permission
- upgrades/version

## 해석

H5P에는 여러 별도 repository/host integration이 있어 전체 품질 체계를 이 몇 repo만으로 판단할 수 없다.

다만 우리가 참고할 때는 **H5P 구조를 가져온다는 이유로 테스트 부족까지 가져오면 안 된다.**

우리 activity schema/manifest에는:
- schema fixture
- migration fixture
- import/export round-trip
- missing dependency
- invalid asset
테스트를 별도로 넣는 것이 적합하다.

---

# 5. Artsteps/Metasteps

내부 test architecture는 공개 확인할 수 없다.

따라서 제품 UX를 참고할 때 우리 쪽에서 별도 테스트 전략이 필요하다.

3D 전시라면 최소:
- 작품 수 증가
- 저사양
- tablet
- collision
- guided route
- direct input interrupt
- reduced motion
- WebGL failure
- 2D fallback
- missing asset
을 테스트한다.

---

# 6. 제품 유형별 추천 테스트

## Local editor
- reload recovery
- quota
- corrupted record
- import/export round-trip
- migration

## Gesture editor
- pointer cancel
- touch
- drag outside canvas
- rapid tool switching
- undo during/after gesture

## Story graph
- broken target
- self link
- merge
- unreachable
- intentional cycle
- forced infinite loop
- large graph rendering

## Realtime collaboration
- 2/3/4 clients
- disconnect/reconnect
- concurrent edit
- stale client
- asset missing
- permission change
- delayed/out-of-order message
- final convergence

## 3D viewer
- no keyboard
- reduced motion
- low GPU
- 20/50/100 artifacts
- invalid viewpoint
- wall collision
- asset timeout
- direct navigation fallback

# 7. Knol에 적용할 품질 원칙

1. 테스트 수보다 **불변조건(invariant)**을 먼저 정의한다.
2. migration은 예전 실제 fixture로 테스트한다.
3. collaboration은 deterministic example + randomized test를 함께 둔다.
4. UI 구조 표시는 accessibility test도 한다.
5. 저장 성공 toast는 실제 durable write 완료와 연결한다.
6. browser storage failure를 정상 시나리오로 본다.
7. 3D에는 항상 non-3D fallback 테스트를 둔다.
