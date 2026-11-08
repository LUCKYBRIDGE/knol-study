# MakeCode / PXT — 기술·실행 흐름

## 기술
- TypeScript
- React
- Blockly 계열
- Monaco
- custom compiler
- simulator runtime
- service worker
- IndexedDB/workspace adapters
- WebUSB
- GitHub extension/package
- target configuration

## 핵심 구조

```text
PXT Core
 + Target
 + Packages/Extensions
 → MakeCode Product
```

## Block definition
```text
TypeScript API
 + //% metadata
 → block definition
 → toolbox
```

같은 API가 text와 block 의미를 공유한다.

## Blocks → text
block AST/metadata를 compiler가 TypeScript representation으로 연결.

## text → Blocks
decompiler가 지원 가능한 TypeScript subset을 block representation으로 변환.

### 중요한 제약
완전한 JavaScript 자유도를 허용하면 안정적 round-trip이 불가능하므로 지원 language subset을 제한한다.

## Compile

```text
Project Source
 → compiler / IR
 ├─ JS/VM backend
 ├─ simulator
 └─ target native backend
     → ARM/UF2 등
```

## Simulator
program을 hardware 없이 즉시 실행.

message simulator로 target-specific simulator도 확장 가능.

## Extension
```text
GitHub package
 → target approval/lookup
 → load/compile
 → API annotations
 → new block categories
 → runtime/simulator/device 기능
```

optional editor extension은 iframe/message boundary를 사용한다.

## Offline
simulator external resources도 service worker cache contract에 포함한다.
