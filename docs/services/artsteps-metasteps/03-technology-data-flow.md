# Artsteps / Metasteps — 관찰 가능한 기술·데이터 흐름

내부 구현은 Unknown이므로 제품 논리 구조만 적는다.

## 논리 모델
```text
Space
├─ spatial geometry
├─ Artifact placement
├─ Interactive points
├─ Navigation links
└─ Publication settings
```

## Portal
```text
Space A
 → Portal target
Space B
```

## Info Point
```text
Spatial anchor
+ title/description/link/category
```

## Web View
```text
3D surface
 → external share URL
 → embedded live web app
```

## Guided Path
같은 공간에 여러 narrative/navigation route를 둘 수 있는 구조.

## Access
공식적으로 member-only / role-based / District 관련 기능이 공개되어 있다.

## Unknown
scene serialization, storage, rendering engine, sync, auth DB는 확인 불가.
