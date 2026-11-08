# H5P — 기술·데이터 흐름

## 기술

- PHP core library
- JavaScript runtime
- PHP/JS editor
- JSON manifest/schema
- host DB/filesystem abstraction
- xAPI/event
- ZIP 기반 .h5p package

## 네 핵심 계약

```text
Host: H5PFrameworkInterface
File: H5PFileStorage
Type: library.json
Content: semantics.json
```

## authoring

```text
semantics.json
  ↓
generic editor / custom widget
  ↓
content params
  ↓
validation
  ↓
host persistence
```

## runtime

```text
Host page
 ↓
Core dependency resolve
 ↓
JS/CSS + content params
 ↓
Content Type runtime
 ↓
events / xAPI / current state
```

## package

```text
.h5p
├─ h5p.json
├─ content/content.json
├─ libraries
└─ assets
```

## upgrade

```text
library version
+ upgrades.js
+ old content params
→ new params
```

## security

일반 author content와 실행 가능한 library code의 신뢰 수준을 분리한다.
library install/update는 더 높은 권한이 필요하다.
