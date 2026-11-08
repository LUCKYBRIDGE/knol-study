# 현재 Knol 저장소 Fit–Gap 분석

검토일: 2026-11-08

외부 도구를 분석한 뒤 실제 현재 main과 대조했다. 이 문서는 "좋아 보이는 기능"이 아니라 **현재 코드에 무엇이 실제로 부족한지**를 판정한다.

## 1. 놀아트 × tldraw / Excalidraw / Artsteps

### 현재 확인
`collab/project.mjs`
- 명시적 project schemaVersion 1
- 전체 프로젝트 유효성 검사
- source/entries/pending/student paint 구조
- 이미지 data URL 용량 제한
- history 저장량 제한

`collab/recovery.mjs`
- IndexedDB 기반 recovery
- 저장 전 validate
- atomic transaction
- quota/blocked/error handling
- 읽을 수 없는 recovery를 자동 삭제하지 않음

`collab/history-policy.mjs`
- undo 50단계
- point/raster budget

즉 놀아트는 이미 **복구·검증·history 안정성 면에서 단순 localStorage 도구보다 상당히 성숙**하다.

### tldraw에서 더 배울 부분
ADAPT:
- 프로젝트 document와 editor session을 명시적으로 별도 타입으로 문서화
- browser/export/share/realtime 대상 필드 표 작성
- 향후 sync 도입 시 source=user/remote 개념
- schema migration sequence

ALREADY:
- IndexedDB recovery
- schema validation
- history budget
- asset 포함 작업 파일

### Excalidraw에서 더 배울 부분
가장 큰 gap은 저장 기술보다 **UI state 저장 정책 표**다.
현재 recovery project에는 작품 데이터가 엄격히 정의되어 있지만, 편집기 UI preference가 어떤 저장 경로에 포함되는지 전체 시스템 정책 문서가 더 명확해질 수 있다.

### Artsteps에서 더 배울 부분
현재 3D는 기술적으로 이미 풍부하다.
- character
- walk/run/jump
- 1/3인칭
- 작품 간 좌우 이동
- interaction distance

하지만 `draw()`에서 카메라는 계속 -z 방향을 보고, `stepWork()`는 작품 x 위치로 이동시키는 수준이다.

**가장 높은 가치의 gap:**  
`ExhibitViewpoint { artworkId, eye, lookAt }` + `Tour { orderedArtworkIds }`.

### 판정
| 제안 | 판정 |
|---|---|
| tldraw SDK 도입 | REJECT 현재 |
| recovery 새로 구축 | ALREADY |
| storage policy matrix | ADAPT |
| UI progressive disclosure | ADAPT |
| guided artwork viewpoint | ADOPT |
| 3D 캐릭터부터 재개발 | DEFER |

---

## 2. 놀프린트 × tldraw / Excalidraw

### 현재 확인
README 및 app.mjs 기준:
- 원본 파일은 브라우저 메모리에서 처리
- 설정만 localStorage에 저장
- 새로고침하면 원본 재선택 필요
- 입력 파일 undo/redo는 메모리 배열
- 인쇄 결과는 원본과 설정에서 파생
- 서버 업로드 API 없음

### 의미
놀프린트는 애초에 "작업 파일 편집기"보다 **일회성 변환 도구**로 설계되어 있다. 따라서 무조건 tldraw식 document persistence를 넣는 것은 요구 범위를 바꾸는 결정이다.

### 사용자 가치가 있는 선택지

#### Option A — 현 구조 유지
장점:
- 개인정보/파일이 브라우저에 오래 남지 않음
- 구현 복잡도 낮음
- 스토리지 quota 문제 적음

단점:
- 새로고침/실수 닫기에서 작업 손실
- 최근 작업 복원이 제한적

#### Option B — opt-in 임시 복구
추천.
- 기본 원본 비영속 유지
- "이 기기에 작업 임시 보관"을 사용자가 켤 때만 IndexedDB blob 저장
- TTL 또는 최근 N개
- 기기 저장임을 명확히 표시
- 개인정보/학교 문서 주의

이 방식이 tldraw의 full autosave보다 놀프린트 목적에 더 맞는다.

### Excalidraw에서 직접 참고할 구조
binary file을 IndexedDB로 따로 저장하고 canvas elements/appState와 분리하는 구조는 Option B에 잘 맞는다.

### 판정
| 제안 | 판정 |
|---|---|
| 모든 원본 자동 영구 보관 | REJECT/DEFER |
| opt-in recovery | ADOPT 후보 |
| 설정/UI 상태와 원본 blob 분리 | ADOPT |
| contextual inspector | ADAPT |
| 실시간 협업 | REJECT 현재 |

---

## 3. 놀스토리 × Twine / H5P

### 현재 구조는 이미 강함
현재 main에는:
- StoryDocument가 canonical SSOT
- schemaVersion 8
- Flow choice/goto
- 2~4 선택지
- score definition
- 조건(all/any, eq/ne/gt/gte/lt/lte)
- effects(add/set)
- conditional routes
- branch choice condition
- runtime/core/renderer 경계
- local save와 .knolstory archive
- migration/validation
이 확인된다.

### 따라서 기존 1차 분석의 "flag/score부터 도입"은 폐기해야 함
이미 구현되어 있다.

### Twine에서 실제로 필요한 것

#### A. Story Map authoring
Flow를 카드 그래프로 보는 보조 편집기/읽기 전용 map.
- 현재 cut
- start
- ending
- incomplete link
- conditional route
- merge point

#### B. 진단의 시각화
현재 `findStoryFlowIssues()`는 끊어진 link/빈 label/cycle을 반환한다.
Twine은 broken link를 지도에서 직접 빨간 선으로 표현한다.
놀스토리도 오류 목록만 제공하기보다 해당 edge/node에 표시하면 교육 사용자에게 더 쉽다.

#### C. cycle 정책 재검토
현재 코드는 DFS back-edge를 모두 `cycle` 문제로 분류하고 "순환하지 않도록 바꾸라"고 안내한다.

Twine에서는 self-link도 정상 표현한다. 인터랙티브 이야기에서 루프는:
- 장소 재방문
- 탐색
- 반복 후 상태 변화
- 선택 회수
에 유효할 수 있다.

따라서 제품 정책이 허용한다면:
- cycle 자체 = warning/info
- 탈출 경로가 전혀 없는 강제 cycle = error 후보
- 상태 변화 없는 자동 goto cycle = 높은 위험
으로 정교화하는 것이 더 적합하다.

**주의:** 현재 놀스토리가 의도적으로 acyclic story만 허용하는 제품 정책이라면 유지 가능. 이것은 코드 오류가 아니라 제품 규칙의 문제다.

### H5P에서 필요한 것
놀스토리 전체를 H5P activity로 만들 필요는 없다.
참고할 부분:
- 콘텐츠 블록 manifest
- media/interaction 종류별 authoring metadata
- `importance` 같은 고급 옵션 단계화

### 판정
| 제안 | 판정 |
|---|---|
| Twine식 엔진 재구축 | REJECT |
| score/condition 새로 추가 | ALREADY |
| story map | ADOPT 후보 |
| broken-link visual diagnostics | ADOPT |
| cycle 정책 검토 | ADAPT |
| H5P type registry 전체 이식 | DEFER |

---

## 4. 놀히스토리 × Twine / H5P

현재는 Phase 0 Foundation이며 구현 전이다. 따라서 외부 패턴의 영향력이 가장 크지만, 동시에 섣부른 구조 고정 위험도 가장 높다.

### 현재 이미 좋은 원칙
- content model first
- stable ID
- source metadata
- fact/interpretation 구분
- domain validation
- persistence 단계적 도입
- 역사 데이터와 UI 분리

### 지금 가져오면 좋은 것

#### H5P에서
Learning Activity를 독립된 UI page가 아니라 type으로 볼 것.
예:
```text
source-inspection
timeline-order
map-compare
perspective-choice
cause-effect
historical-scenario
```

그러나 Product Spec의 **핵심 경험 1개가 확정되기 전 registry를 구현하지 않는다.**

#### Twine에서
'분기형 역사 체험'이 핵심 경험으로 선택될 경우:
- historical event graph
- learner choice state
- historical constraint
를 분리.

하지만 실제 사건의 결과를 마음대로 바꾸는 generic branching engine보다:
```text
historical invariant
+ learner perspective/decision
+ consequence explanation
```
구조가 교육적으로 더 적합하다.

### 추천
현재 Phase 0에서는 코딩보다 **한 개 사건 thin vertical prototype 설계**가 우선이다.
Twine과 H5P는 "어떤 엔진을 쓸지"가 아니라 첫 시나리오 설계의 참고 자료로 사용한다.

---

## 5. 전체 우선순위 재판정

### P0
1. 놀아트 작품별 viewpoint + guided route MVP
2. 놀스토리 Story Map/진단 UX 설계 및 cycle 정책 결정
3. 놀프린트 opt-in recovery 필요성 사용자 시나리오 검증
4. 놀아트/놀프린트 저장 대상 정책표(browser/export/share) 작성

### P1
5. 놀아트 UI Always/Contextual/Advanced inventory
6. 놀프린트 contextual flow UI
7. 놀히스토리 첫 학습 경험 결정 후 activity contract 초안

### P2
8. 서비스 간 공통 persistence 모듈 추출 여부 평가
9. reusable activity/block registry
10. 서버 sync/협업

공통 모듈은 세 서비스에서 동일 문제가 실제 반복된 뒤 추출한다.
