# Microsoft MakeCode / PXT 심층 분석

검토일: 2026-11-08  
근거: 공식 `microsoft/pxt` master + MakeCode docs  
근거 수준: **A1/A2**

# 1. MakeCode의 진짜 핵심

MakeCode는 block editor가 아니다.

PXT는 **초보자용 프로그래밍 경험을 여러 하드웨어/도메인에 맞게 생성하는 framework**다.

공식 README의 핵심 기능:
- Blockly code editor
- text format converter
- Monaco editor
- TypeScript로 새 block 확장
- ARM Thumb code emitter
- CLI package manager

따라서 구조를 이렇게 보는 것이 정확하다.

```text
PXT Framework
      ↓
Target
  micro:bit / Arcade / Maker / Minecraft ...
      ↓
Project
      ↓
Blocks ↔ TypeScript
      ↓
Simulator / Compiler
      ↓
Device / Browser Runtime
```

---

# 2. Repository Topology

```text
pxt/
├─ pxtblocks/
├─ pxtcompiler/
├─ pxteditor/
├─ pxtlib/
├─ pxtsim/
├─ pxtservices/
├─ pxtpy/
├─ pxtrunner/
├─ webapp/
├─ react-common/
├─ cli/
├─ skillmap/
├─ teachertool/
├─ tutorialtool/
├─ multiplayer/
├─ docs/
└─ libs/
```

구조 자체가 framework의 범위를 보여준다.

---

# 3. Target가 가장 중요한 확장 경계

`pxtarget.json`은 특정 MakeCode product의 구성 manifest다.

Target는 대략:
- branding/theme
- compile
- simulator
- blocks
- bundled packages
- board definition
- documentation
- app behavior
를 결정한다.

즉 micro:bit MakeCode와 Arcade는 완전히 별도 editor codebase가 아니라 **PXT core + Target configuration/package** 구조다.

---

# 4. 하나의 API definition이 Block과 TypeScript를 연결

MakeCode의 강한 구조.

TypeScript API:

```typescript
//% block="show number $v"
export function showNumber(v: number) {}
```

annotation이 block definition이 된다.

즉:

```text
TypeScript API
   ↓ metadata annotation
Block definition
   ↓
Blockly UI
```

### 왜 매우 중요?

Block용 runtime API와 text-code API를 별도로 구현하지 않는다.

**하나의 semantic API를 두 표현이 공유**한다.

---

# 5. Block metadata가 UX까지 정의

annotation으로:
- block label
- category
- color
- weight/order
- Advanced 여부
- group
- input layout
- min/max/default
- shadow block
- localization
- ariaLabel
등을 정의한다.

즉 H5P semantics와 비슷하게:

```text
Programming API
+
Authoring metadata
=
Beginner UI
```

로 볼 수 있다.

---

# 6. Progressive Disclosure가 metadata 수준에서 존재

대표 기능:

### Category advanced
고급 category를 Advanced 아래로 이동.

### Block advanced
rare block을 More... 아래로 이동.

### expandable arguments
자주 쓰지 않는 parameter를 기본적으로 접음.

이것은 단순 CSS 숨김이 아니라 **API author가 난이도를 선언**한다.

### 교육 UX 교훈

고급 기능을 별도 제품으로 나누지 않고 같은 semantic API 안에서 단계적으로 노출한다.

---

# 7. Blocks ↔ Text 전환

PXT underlying language는 제한된 TypeScript subset이다.

Block workspace와 text code가 같은 program semantics로 연결된다.

### 장점
- 초보자 → 코드 학습 progression
- block에서 시작해 text로 이동 가능
- 하나의 compiler/runtime

### 어려움
모든 자유로운 JavaScript가 block으로 round-trip 가능한 것은 아니다.

따라서 **양방향 표현 가능한 language subset**을 의도적으로 제한한다.

---

# 8. Simulator는 부가 preview가 아니라 주요 runtime

공식 제품 설명:
학생은 hardware가 없어도 browser simulator에서 즉시 실행할 수 있다.

구조:

```text
Program
  ↓ compile/transpile
Simulator Runtime
  ↓
Virtual Board / Game / Device
```

Target마다 simulator를 정의할 수 있다.

외부 message simulator도 channel별 URL로 등록 가능.

---

# 9. Offline을 target asset 정책으로 해결

Simulator가 external resource를 사용하면 offline이 깨질 수 있다.

PXT는 target simulator service worker가 cache할 URL을 지정할 수 있게 한다.

즉 "PWA 지원"을 단순 global feature로 보지 않고 **target resource dependency도 offline contract에 포함**한다.

---

# 10. Extension 구조

Extension:
- dynamic/static library mechanism
- GitHub에서 dynamic load 가능
- target가 approved whitelist 제공 가능
- TypeScript APIs
- Blocks 자동 추가
- versioning/localization/docs
- asset pack
- optional editor extension

Editor extension은 iframe에 로드하고 message로 project/serial data와 통신한다.

## 중요한 security boundary

```text
Core Editor
  ↕ message contract
Extension iframe
```

임의 extension UI를 editor DOM 안에 직접 섞지 않는다.

---

# 11. Extension 설치가 곧 Toolbox 확장

사용자가 Neopixel extension을 추가하면:
- package load
- compile
- API annotation 해석
- 새로운 block category 등장

즉 extension model이 **runtime + authoring UI를 동시에 확장**한다.

H5P와 비교 가치가 높다.

```text
H5P
library → author form + learner runtime

MakeCode
extension → block toolbox + simulator/device runtime
```

---

# 12. Hardware compile

PXT는 target에 따라:
- browser compile
- ARM machine code
- UF2 output
등을 지원한다.

학생 관점:

```text
Blocks
 ↓
Program
 ↓
Simulator
 ↓
Download
 ↓
Physical Device
```

이 feedback loop가 매우 중요하다.

---

# 13. 기능을 풀어낸 UX

## 초보자
Blocks + simulator.

## 성장 단계
JavaScript/Python.

## advanced
Extensions, GitHub, project tools.

## developer
Target creation.

즉 **같은 생태계 안에 learner maturity ladder**가 존재한다.

---

# 14. 테스트

공식 README:
- node tests
- browser tests
- root tests directory

repository에는 compiler/editor/simulator 등 다양한 계층이 분리되어 있어 target-specific test와 core test를 나누기 쉽다.

향후 더 깊게 fixture/round-trip 테스트를 조사할 가치가 있다.

---

# 15. 장점

1. Blocks와 text가 같은 semantic API를 공유한다.
2. target architecture로 여러 제품을 하나의 framework에서 만든다.
3. simulator가 학습 feedback을 즉시 제공한다.
4. extension이 toolbox/runtime를 함께 확장한다.
5. progressive disclosure가 API metadata에 포함된다.
6. offline/hardware까지 고려한다.
7. 교육용 난이도 progression이 구조적으로 잘 설계돼 있다.

---

# 16. Trade-off

1. PXT 자체가 매우 큰 framework.
2. block↔text round-trip 때문에 language 자유도가 제한된다.
3. target/extension/compiler/simulator compatibility가 복잡하다.
4. hardware target마다 예외가 생긴다.
5. annotation metadata가 많아 API 작성 규칙이 복잡하다.

---

# 17. Knol에서 특히 배울 것

## 교육 활동
**한 개념을 쉬운 표현과 어려운 표현으로 동시에 제공.**

예:
```text
Simple UI
  ↔
Advanced Configuration
```

## 놀스토리/놀히스토리
단계적으로:
- 선택지 builder
- 조건 builder
- advanced logic
같은 progression을 만들 때 참고.

## 향후 피지컬 코딩
PXT 자체 도입 여부와 별개로:
- simulator first
- target abstraction
- extension whitelist
는 매우 유용.

---

# 18. 추가 분석 과제

- 실제 Blocks ↔ TypeScript decompile pipeline
- AST
- project file format
- save/share
- target merge
- simulator message protocol
- compiler pipeline
- UF2
- extension sandbox/security
- tutorial engine
- skillmap
- accessibility
- MakeCode Arcade runtime
- micro:bit target 비교

---

# 19. Source Evidence

- `microsoft/pxt/README.md`
- `pxtarget.json`
- `docs/targets/pxtarget.md`
- `docs/targets/simulator.md`
- `docs/defining-blocks.md`
- `docs/extensions.md`
- official MakeCode About/Docs
