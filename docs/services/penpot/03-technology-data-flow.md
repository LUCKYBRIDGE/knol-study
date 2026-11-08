# Penpot — 기술·데이터 흐름

## 공식 architecture

```text
Browser
  ↓
Frontend: ClojureScript + React
  ↕ Web Worker
  ↓ RPC
  ↕ WebSocket
Backend: Clojure/JVM
  ├─ PostgreSQL
  └─ Redis pub/sub

Frontend
  ↓ HTTPS
Exporter: ClojureScript/Node
  ↓ Puppeteer
Headless Chrome
```

## 데이터 원칙

공식 Data Guide:
- 가능하면 attribute optional
- attribute 없음 = default behavior
- null과 absence를 다른 상태로 기대하지 않음
- export 가능한 key naming 사용

### 장점
새 필드가 없어도 기존 문서가 default behavior로 계속 동작하게 하여 migration 부담을 줄인다.

## migration
file version + migration sequence.

```text
old file
 → load
 → missing migrations 순차 실행
 → current in-memory model
 → 다음 save에서 최신 version
```

## Component sync

```text
Main Component
 → sync group
 → Instance

Instance local edit
 → touched group
 → 이후 main propagation 차단
```

## Render / Export / Import

```text
Penpot Shape
 → shared shape renderer
 → SVG

SVG + metadata
 → import parser
 → Penpot Shape
```

화면과 export가 최대한 같은 renderer 의미를 공유한다.

## Realtime
공식 architecture:
- frontend/backend WebSocket
- backend/Redis pub-sub

정확한 conflict algorithm은 별도 세부 분석 대상.

## 기술적 핵심
Penpot은 design object를 web-standard representation에 가깝게 유지해:
- export
- inspect
- code generation
- responsive layout
사이의 translation gap을 줄인다.
