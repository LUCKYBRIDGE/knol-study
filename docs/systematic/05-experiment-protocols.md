# 적용 실험 프로토콜

분석을 실제 개발 결정으로 바꾸기 위한 최소 실험이다.

## E1 — 놀아트 Guided Viewpoint

### 가설
자유 이동만 제공하는 것보다 작품별 viewpoint + 이전/다음 이동을 제공하면 3D 조작 경험 없이도 전체 작품을 감상할 수 있다.

### 구현
- 현재 artwork mount마다 viewpoint 계산/저장
- `goToArtwork(index)`
- eye/lookAt
- 이전/다음
- reduced motion에서는 즉시 이동

### 측정
- 작품 1→마지막까지 도달 가능
- 직접 WASD 사용 횟수
- 잘못된 작품 선택
- 카메라 벽 관통
- 태블릿 완료 여부

### 통과
WASD 없이 모든 작품 접근 가능. 2D 목록 fallback 유지.

---

## E2 — 놀스토리 Story Map

### 가설
텍스트 목록보다 전체 flow를 지도에서 보면 incomplete link와 분기 구조 수정이 쉬워진다.

### 범위
**읽기 전용 map부터 시작.**
- line nodes
- choice/goto edges
- ending
- incomplete target
- score/branch condition badge
- 클릭하면 기존 편집기로 이동

드래그로 실제 flow를 바꾸는 기능은 2단계.

### 검증
- 기존 StoryDocument를 수정하지 않고 map 생성
- 100/500 cut에서 성능
- broken link 위치 즉시 찾기
- screen reader에는 별도 구조 목록 제공

---

## E3 — Cycle Policy Fixture

현재 `findStoryFlowIssues`가 cycle을 전부 문제로 본다.

fixture:
1. A→B→A, 선택으로 C 종료 가능
2. A→B→A, 탈출 없음
3. A→B→A, 점수가 증가해 3회 후 C
4. A→A self-link + '그만하기' 선택
5. 자동 goto A→B→A, 상태 변화 없음

각 case를:
- valid
- warning
- error
로 제품 수준에서 먼저 결정한 뒤 validator를 수정한다.

---

## E4 — 놀프린트 Opt-in Recovery

### 가설
원본 자동 영구 저장 대신 opt-in 임시 복구가 작업 손실과 privacy 사이의 균형이 좋다.

### Prototype
- 체크/토글: "이 기기에 작업 임시 보관"
- File/Blob → IndexedDB
- 설정 JSON 별도
- lastUsed
- TTL
- 3개 한도
- "모두 삭제"

### 실패 실험
- 30MB/100MB 이미지
- quota exceeded
- private browsing
- 저장 중 탭 종료
- 파일 복구 후 object URL 재생성

### 결정
복구 성공률과 사용자 필요성이 낮으면 기능을 제거한다.

---

## E5 — State Storage Policy Audit

놀아트/놀프린트의 state field를 다음 표로 분류한다.

| Field | browser | export | share | realtime | history |
|---|---:|---:|---:|---:|---:|
| 작품 객체 | ✓ | ✓ | ✓ | 후보 | ✓ |
| zoom | ✓ |  |  |  |  |
| selected id | ✓? |  |  | presence 후보 |  |
| panel open | ✓? |  |  |  |  |
| 원본 blob | opt | 파일 포함 | 필요 시 | 별도 |  |

실제 필드 전체를 채워 문서화한 뒤 구현과 일치 여부 테스트.

---

## E6 — H5P식 Activity Manifest Spike

놀히스토리가 아니라 **기존 놀퀴즈의 실제 3종**을 대상으로 실험하는 것이 더 적합.

```ts
type ActivityManifest = {
  id: string
  version: number
  dataSchema: unknown
  capabilities: {
    scored: boolean
    media: boolean
    keyboardRequired: boolean
  }
  authorComponent: string
  playerComponent: string
}
```

통과 조건:
네 번째 타입을 추가할 때 저장/라우팅/import/export core를 거의 수정하지 않음.

---

## E7 — UI Progressive Disclosure Test

대상: 놀프린트, 놀아트.

과제:
- 이미지 업로드
- 자르기
- 분할 변경
- undo
- 테셀레이션 변형
- 전체화면

비교:
A: 현재 UI
B: Always/Contextual/Advanced 재배치

측정:
- 완료율
- 잘못 클릭
- 도움말 요청
- 작업 시간
- 결과 오류

실측 전에는 "더 쉬워졌다"고 결론 내리지 않는다.
