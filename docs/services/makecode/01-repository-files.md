# MakeCode / PXT — 저장소·폴더·파일 구조

공식 `microsoft/pxt`.

```text
pxt/
├─ webapp/
├─ pxtblocks/
├─ pxtcompiler/
├─ pxteditor/
├─ pxtlib/
├─ pxtsim/
├─ pxtservices/
├─ pxtpy/
├─ pxtrunner/
├─ react-common/
├─ cli/
├─ skillmap/
├─ teachertool/
├─ tutorialtool/
├─ multiplayer/
├─ libs/
└─ docs/
```

## webapp

실제 핵심 파일:
- app.tsx
- projects.tsx
- workspace.ts
- blocks.tsx
- monaco.tsx
- srceditor.tsx
- headerbar.tsx
- editortoolbar.tsx
- simtoolbar.tsx
- simulator.ts
- compiler.ts
- extensionManager.ts
- extensionsBrowser.tsx
- cloudsync.ts
- idbworkspace.ts
- browserworkspace.ts
- share.tsx
- tutorial.tsx
- debugger*
- serial.tsx
- webusb.tsx

## pxtblocks

```text
pxtblocks/
├─ builtins/
├─ compiler/
├─ fields/
├─ plugins/
├─ contextMenu/
├─ loader.ts
├─ toolbox.ts
├─ importer.ts
├─ render.ts
├─ diff.ts
└─ xml.ts
```

field editor가 매우 풍부하다:
- LED matrix
- sprite
- tilemap
- music
- piano roll
- color
- position
- dropdown
- image 등.

## pxtcompiler

```text
emitter/
├─ decompiler.ts
├─ transpile.ts
├─ backjs.ts
├─ backthumb.ts
├─ backvm.ts
├─ ir.ts
├─ service.ts
└─ languagehost.ts
```

## pxtsim
- runtime
- debugger
- debug protocol
- sim driver
- board visuals
- device state
- audio
- accessibility

## Target
실제 MakeCode 제품은 PXT core에 target repo/config를 결합한다.
예:
- pxt-microbit
- pxt-arcade
