# 91. 구현 실험 백로그

이 문서는 분석 결과를 실제 코드 개선으로 옮길 때 사용할 실험 목록입니다. 대규모 리팩터링보다 **작고 검증 가능한 실험**부터 진행합니다.

## P0 — 체감 개선

### K-001 놀아트 3D guided tour

**영감:** Artsteps

구현 최소 범위:

- 작품별 viewpoint 저장
- 이전/다음 작품
- 이동 보간
- 도착 시 작품 정면 바라보기
- 작품 설명 카드

완료 조건:

- 키보드 이동을 전혀 쓰지 않아도 모든 작품 관람 가능
- 모바일에서도 동일하게 동작
- 20개 이상 작품에서 순서 이동 가능

### K-002 편집기 UI 노출 계층 정리

**영감:** Excalidraw

대상:

- 놀아트
- 놀프린트

작업:

모든 버튼/옵션을:

- Always
- Contextual
- Advanced

로 분류하고 초기 화면의 Always 기능 수를 줄입니다.

완료 조건:

- 초보 사용자가 핵심 작업을 위해 고급 패널을 열 필요 없음
- 현재 선택과 관계없는 설정이 기본 노출되지 않음

### K-003 공통 autosave/recovery 규칙

**영감:** tldraw, Excalidraw

최소 사양:

- IndexedDB
- schemaVersion
- 마지막 정상본
- 최신 draft
- 저장 중/저장됨 상태
- 새로고침 복구

완료 조건:

- 작업 중 강제 새로고침 후 데이터 손실 없음
- 저장 포맷 버전 변경 테스트 통과

## P1 — 구조 개선

### K-101 놀스토리 graph validator

**영감:** Twine

검사:

- 시작 노드
- 연결되지 않은 선택
- 도달 불가 노드
- 출구 없는 노드
- 순환 경로

완료 조건:

- 편집 화면에서 문제 노드를 바로 선택 가능

### K-102 story state v1

**영감:** Twine

1차 지원:

- Boolean flag
- Number score

UI:

- 선택 시 값 변경
- 조건에 따라 선택지 표시

제외:

- 임의 JavaScript
- 자유식 expression 입력

### K-103 document/session 분리

**영감:** tldraw

놀아트에서 먼저 실험:

- document: 실제 작품
- session: 선택/카메라/줌/패널

완료 조건:

- session을 삭제해도 작품 결과가 동일
- 다른 기기에서 document만 불러와 정상 렌더링

## P2 — 확장 기반

### K-201 activity registry prototype

**영감:** H5P

기존 활동 3종을 공통 interface로 래핑합니다.

예:

```text
type
version
schema
defaultData
validate
authorRenderer
playerRenderer
migrate
```

완료 조건:

- 네 번째 activity를 추가할 때 공통 저장/라우팅 코드를 수정하지 않거나 최소 수정

### K-202 재사용 블록

**영감:** H5P

범위:

- 문항 하나 저장
- 활동 블록 저장
- 다른 프로젝트에 삽입

### K-203 export/import 규격

**영감:** H5P, Excalidraw

1단계:

- JSON

2단계:

- media asset가 필요하면 ZIP 패키지

필수:

- format
- version
- type
- data
- metadata

## P3 — 나중에

### K-301 실시간 협업

**영감:** tldraw, Excalidraw

선행 조건:

- document/session 분리 완료
- autosave 안정화
- conflict 정책
- 사용자 식별/권한 정책

실시간 협업은 선행 구조가 잡히기 전에 넣지 않습니다.

### K-302 고급 3D 캐릭터

**영감:** Artsteps 분석에서의 역우선순위

선행 조건:

- guided tour 완료
- 모바일 조작 안정화
- 카메라/충돌 안정화

그 뒤에만:

- avatar 교체
- walk/idle blend
- 방향 전환
- 필요 시 점프

를 검토합니다.

## 실험 기록 규칙

각 실험을 실제 저장소에서 진행할 때 이 문서 또는 별도 issue에 다음을 기록합니다.

```text
문제
가설
변경
테스트 환경
결과
유지/폐기 결정
다음 단계
```

분석 문서는 아이디어 저장소이고, 이 백로그는 검증 가능한 변경 목록입니다.
