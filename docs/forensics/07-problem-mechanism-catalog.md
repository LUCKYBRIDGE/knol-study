# 문제 → 설계 메커니즘 카탈로그

분석한 제품들이 실제로 사용한 해법을 문제 중심으로 재분류한다.

| 제품 문제 | 대표 메커니즘 | 강한 참고 사례 | 적용할 때 | 주의 |
|---|---|---|---|---|
| 여러 단계 gesture가 꼬임 | hierarchical state machine | tldraw | drag/resize/mode 전환 | 작은 UI에는 과함 |
| UI/shortcut 동작 불일치 | command/action registry | Excalidraw | 동일 기능 진입점이 많음 | action이 너무 비대해질 수 있음 |
| 객체 종류가 계속 늘어남 | data + behavior util | tldraw ShapeUtil | custom content/object | polymorphism 과설계 |
| 설정 폼 반복 | schema-driven editor | H5P | 유사 옵션이 많은 activity | 핵심 UX까지 generic화 금지 |
| 관계 구조를 이해하기 어려움 | visual graph | Twine | branching/workflow | graph jargon 숨기기 |
| 연결 대상이 없어서 작업 흐름 끊김 | destination auto-create | Twine | 분기 저작 | 자동 생성 orphan 정책 필요 |
| 구조 오류 찾기 어려움 | inline visual diagnostics | Twine | graph/story/workflow | 오류 등급 명확화 |
| 개인 UI 상태가 파일에 섞임 | destination policy | Excalidraw | editor export | field audit 필요 |
| 협업 커서가 문서에 저장됨 | lifecycle scope | tldraw | realtime editor | scope 설계 먼저 |
| drag 중간 상태가 undo에 쌓임 | durable/ephemeral change | Excalidraw | continuous gesture | capture 경계 테스트 |
| binary가 JSON을 비대하게 만듦 | asset/blob separation | tldraw, Excalidraw | 이미지/영상 | orphan/GC 필요 |
| 새로고침 작업 손실 | local persistence | 둘 모두 | 편집 도구 | privacy/quota 고려 |
| 네트워크 전달=저장으로 착각 | realtime/durable separation | Excalidraw | collaboration | 두 실패 상태 별도 UX |
| 여러 사용자가 충돌 | server rebase or reconciliation | tldraw / Excalidraw | realtime | 요구에 맞는 모델 선택 |
| 구버전 파일이 깨짐 | migration sequence | tldraw/H5P | 장기 콘텐츠 | fixture 필수 |
| 구버전 client가 협업 파괴 | protocol/schema compatibility | tldraw | live sync | 명시적 upgrade error |
| 다양한 LMS 지원 | host interface | H5P | platform ecosystem | abstraction 비용 |
| 활동 유형 추가 때 core 수정 | versioned content type | H5P | activity 3종 이상 반복 | 너무 일찍 추출 금지 |
| user data와 plugin code 혼동 | trust boundary | H5P | extension ecosystem | code install 권한 분리 |
| 이어하기 | state contract | H5P | learner runtime | 타입별 지원 확인 |
| 초보자가 graph 개념 어려움 | domain metaphor | Twine | story branching | 고급 기능 단계화 |
| 3D 사용자가 길을 잃음 | guided path/viewpoint | Artsteps | gallery/museum | free roam와 조화 |
| 3D engine이 너무 전문적 | domain primitives | Artsteps | nontechnical creator | domain model 정확성 중요 |
| 전시 media 종류가 많음 | artifact abstraction | Artsteps | heterogeneous content | type별 behavior 필요 |
| 콘텐츠를 다른 환경으로 이동 | portable package | H5P/Twine/Excalidraw | reuse | version/dependency 포함 |
| 회귀가 무작위 상황에서 발생 | fuzz/randomized tests | tldraw | sync/graph state | seed 기록 |
| 접근성 회귀 | automated a11y component tests | Twine | UI component | 수동 검증도 필요 |

# 메커니즘 선택 순서

새 기능에서 먼저 기술 이름을 고르지 않는다.

```text
실제 실패/불편
  ↓
문제의 종류
  ↓
상태/데이터 수명주기
  ↓
적합한 메커니즘 후보
  ↓
가장 작은 prototype
  ↓
실측/회귀 테스트
```

# 서로 같이 쓰면 좋은 조합

## 복잡한 편집기

```text
tldraw State Machine
+
Excalidraw Action Registry
+
Excalidraw destination storage policy
```

## 교육 활동 플랫폼

```text
H5P Type Manifest
+
H5P Semantics
+
전용 핵심 Author UI
```

generic editor를 모든 화면에 강요하지 않는다.

## 분기 저작

```text
기존 domain/runtime
+
Twine Story Map
+
visual diagnostics
+
state debugger
```

## 3D 전시

```text
Artifact
+
Placement
+
Viewpoint
+
Guided Path
+
2D fallback
```
