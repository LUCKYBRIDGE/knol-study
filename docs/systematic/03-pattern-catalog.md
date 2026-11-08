# 재사용 가능한 설계 패턴 카탈로그

## P-01 Canonical Document
**출처:** tldraw, KnolStory  
결과물의 유일한 진실 원천을 명확히 한다.

적용 조건:
- 파일 export/import 필요
- 버전 migration 필요
- 여러 화면이 같은 콘텐츠를 편집

피해야 할 것:
- DOM을 저장 포맷으로 사용
- 여러 state store에 같은 콘텐츠 중복

---

## P-02 Lifecycle Scope
**출처:** tldraw

```text
document  = 저장/공유할 실제 콘텐츠
session   = 사용자별 작업 화면
presence  = 협업 순간 상태
```

적용:
- 편집기
- 공동 작업
- 복잡한 viewer

---

## P-03 Destination Storage Policy
**출처:** Excalidraw

같은 AppState라도 저장 목적지별 포함 여부를 다르게 정의한다.

```text
field                 browser export server realtime history
zoom                    yes     no     no      no       no
selectedIds             yes     no     no      maybe    no
grid                     yes     yes    yes     yes      yes?
content element          yes     yes    yes     yes      yes
cursor                    no     no     no      yes      no
```

실제 값은 서비스별로 결정.

---

## P-04 Asset/Data Separation
**출처:** tldraw, Excalidraw

메타데이터 JSON과 binary blob을 분리한다.

장점:
- 대용량 serializer 부담 감소
- blob별 lifecycle
- 사용하지 않는 asset garbage collection
- 네트워크/IndexedDB 최적화

적용:
- 놀아트
- 놀프린트 opt-in recovery
- 놀스토리 media archive

---

## P-05 Versioned Manifest
**출처:** H5P

타입 자체가:
- id
- version
- dependencies
- runtime
- editor
를 명시한다.

적용:
향후 놀퀴즈 activity registry.

---

## P-06 Schema-driven Authoring Metadata
**출처:** H5P

schema가:
- validation
- importance
- widget
- showWhen
까지 표현할 수 있다.

적용:
교사에게 고급 옵션을 숨기되 데이터 모델은 하나로 유지.

주의:
모든 UI를 generic form으로 만들지 않는다.

---

## P-07 Author / Runtime Separation
**출처:** H5P, Twine, KnolStory

제작 데이터와 실행 상태를 분리한다.
놀스토리는 이미 강하게 채택.

---

## P-08 Visual Structural Diagnostics
**출처:** Twine

오류를 별도 목록에만 두지 말고 구조 자체 위에 표시.
- broken edge
- empty node
- start
- warning badge
- conditional edge

적용:
놀스토리 Story Map.

---

## P-09 Guided Viewpoint
**출처:** Artsteps guide point 개념 + 놀아트 자체 설계

3D 콘텐츠 위치와 관람 위치를 분리.
```text
ArtifactTransform
Viewpoint.eye
Viewpoint.lookAt
Tour.order
```

---

## P-10 Opt-in Recovery
**출처:** local-first 패턴을 도구 목적에 맞게 변형

모든 도구가 full persistence가 필요한 것은 아니다.
일회성 파일 변환 도구는:
- 기본 비영속
- 사용자가 원할 때만 기기 복구
가 개인정보·복잡도 측면에서 더 적절할 수 있다.

놀프린트 후보.

---

## P-11 Recoverable Failure
**출처:** 놀아트 현 구현, tldraw/Excalidraw

저장 실패 시:
- 기존 정상본을 지우지 않는다.
- unreadable recovery를 자동 삭제하지 않는다.
- quota 문제를 사용자에게 설명.
- export 탈출구 제공.

---

## P-12 Explicit Product Constraint
**출처:** Twine ↔ KnolStory 비교

기술적으로 가능한 것을 반드시 지원하지 않는다.
예: cycle.

정책은:
- 기술 제약
- 교육 UX 제약
- 제품 의도
를 구분해 기록한다.
