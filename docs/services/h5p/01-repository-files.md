# H5P — 저장소·파일 구조

H5P는 monorepo 하나가 아니라 ecosystem이다.

```text
h5p-php-library
  Core / validation / storage contracts / export

h5p-editor-php-library
  generic authoring editor

h5p-multi-choice
h5p-interactive-video
h5p-branching-scenario
...
  content type libraries

Host integration
  Moodle / WordPress / Drupal / H5P.com 등
```

## Content Type 기본 구조

```text
ContentType/
├─ library.json
├─ semantics.json
├─ js/ or src/
├─ css/
├─ language/
├─ presave.js
└─ upgrades.js
```

## Editor

```text
h5p-editor-php-library/
├─ h5peditor.class.php
├─ h5peditor-ajax.class.php
├─ h5peditor-storage.interface.php
└─ scripts/
   ├─ h5peditor-form.js
   ├─ h5peditor-group.js
   ├─ h5peditor-list.js
   ├─ h5peditor-library.js
   ├─ h5peditor-file.js
   ├─ h5peditor-image.js
   ├─ h5peditor-av.js
   ├─ h5peditor-html.js
   ├─ h5peditor-text.js
   ├─ h5peditor-number.js
   ├─ h5peditor-boolean.js
   ├─ h5peditor-select.js
   └─ h5peditor-semantic-structure.js
```

## Core classes

- H5PCore
- H5PFrameworkInterface
- H5PFileStorage
- H5PValidator
- H5PContentValidator
- H5PStorage
- H5PExport

핵심은 host DB/file 시스템을 core가 직접 소유하지 않는다는 점이다.
