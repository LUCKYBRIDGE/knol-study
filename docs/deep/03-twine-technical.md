# Twine — 분기 엔진 심층 검토

검토일: 2026-11-08. **Twine 2 공식 reference 기반(A), 놀스토리 실제 코드와 호환 확인은 미실시.**

## 1. 저작 모델과 플레이 모델
Twine 편집기의 Story Map은 passage를 카드로, 연결을 방향 화살표로 보여준다. 검색은 제목/본문 fuzzy matching, 태그 색상, 빈 passage, 시작 passage, broken link 표시를 지원한다. [공식 Story Map](https://twinery.org/reference/en/editing-stories/navigating.html).

- passage: 사람이 이해하기 쉬운 장면 단위
- link: 대상 passage로의 방향 간선
- variables: 이전 선택이나 획득 상태를 이어서 유지
- story format: 표시 및 실행 의미를 결정
- publish: 브라우저에서 실행할 HTML 결과물; Twee export도 지원 [공식 export](https://www.twinery.org/reference/en/story-library/exporting.html)

## 2. Twine의 가장 유용한 UX는 '오류를 지도에 보여주는 것'
확인된 표시:
- 시작 passage 녹색 로켓 아이콘
- 대상이 없는 링크 빨간 선/금지 기호
- 내용이 없는 passage 점선·반투명 카드
- 태그의 시각적 분류
- 대형 지도에서 검색·중앙 정렬

**놀스토리로의 변환 제안**:
- 필수 오류: 시작 노드 누락, 없는 목적지, 데이터 깨짐.
- 권고 경고: 도달 불가능 장면, 내용 없는 장면, 선택지 없는 중간 장면.
- **루프 자체는 오류 아님**. 회귀하는 이야기·마을 탐험·상태 변화 재방문은 정상적인 게임플레이일 수 있음. 단 '상태 변화 없이 강제 반복되는 탈출 불가 컴포넌트'는 정적/실행 테스트로 경고.

## 3. 권장 데이터 구조(자체 제안)
```ts
type Story = {
 schemaVersion: number;
 startNodeId: string;
 nodes: Record<string, { id: string; title: string; blocks: ContentBlock[] }>;
 edges: Record<string, { id: string; from: string; to: string; label: string; condition?: Condition; effects?: Effect[] }>;
 variableDefs: Record<string, { kind: "boolean" | "number"; initial: boolean | number }>;
};
type Condition = { variableId: string; op: "eq" | "gte" | "lte"; value: boolean | number };
type Effect = { variableId: string; op: "set" | "add"; value: boolean | number };
```
자연어 UI에서 편집하고, 데이터에 JavaScript 함수를 저장하지 않는다. 변수 이름은 표시용이고 내부 비교/참조는 `variableId`로 연결하여 이름 변경 시 조건이 깨지지 않게 한다.

## 4. 실행 의미를 먼저 고정
조건을 추가하기 전에 다음 정책을 정해야 한다.
1. 조건이 false인 선택지를 숨길지 비활성화할지.
2. edge effect를 **선택이 확정된 한 번**만 실행할지, 재방문마다 실행할지.
3. 같은 노드를 다시 방문하면 콘텐츠가 재실행되는지.
4. 되돌아가기는 상태 스냅샷을 복구할지, navigation만 되돌릴지.
5. 저장/이어서하기에서 현재 노드·변수·방문 이력·플레이어 버전을 어떻게 보존할지.
6. 조건이 전부 false일 때 '막힘'을 어떻게 안내할지.
7. 랜덤 요소가 생기면 재현을 위해 seed가 필요한지.

## 5. 놀히스토리에서 추가할 역사 검증 층
```text
Story content (사건·자료·선택지)
  ↓
Historical constraint validator (시대/사건 선후/도구·용어)
  ↓
Narrative runtime (몰입·분기)
  ↓
Learning feedback (선택 이유·자료 근거·오개념)
```
역사적 사실은 **이야기 몰입을 위한 임의 변수와 분리**한다. '실제 사건은 바꿀 수 없으나 인물의 관점과 선택은 체험할 수 있다'는 제약의 표현이 교육 목적에 맞다. 실제 시대 고증 검증은 별도 사료 확인이 필요하다.

## 6. 알고리즘 검증 계획
- BFS/DFS로 시작점에서 도달 가능한 모든 노드 확인.
- 존재하지 않는 노드 참조 검사.
- SCC(Tarjan/Kosaraju 등)로 순환 컴포넌트 탐지하되 무조건 오류 처리하지 않음.
- 시작~결말 경로가 있는지 검사. 조건이 있는 경우 정적 경로 존재만으로 실제 도달 가능하다고 확정하지 않음.
- 조건/효과 조합은 경계값(-1, 0, 1), 같은 edge 반복 선택, 뒤로 가기를 테스트.

## 7. 제작기 UX 시나리오
| 교사 행동 | 시스템 피드백 |
|---|---|
| 장면 추가 | 명확한 새 카드 및 연결 손잡이 |
| 선택지 연결 | 다음 장면 미리보기, 빈 장면 자동 생성은 확인 후 |
| 조건 설정 | '용기가 2 이상이면 보여요' |
| 장면 삭제 | 연결 영향 수 표시 및 안전한 복구 |
| 미리보기 | 현재 변수·방문 기록·선택 조건 설명 |
| 파일 공유 | 실행용과 편집용 형식 분리 |

## 8. 직접 코드 재사용에 대한 주의
[Twine 저장소](https://github.com/klembot/twinejs)의 라이선스 및 포함된 story format의 라이선스를 별도로 확인해야 한다. Twine 저작 모델에서 아이디어를 얻는 것과 코드를 복사하는 것은 다르다. 공식 reference의 macro/조건 구문은 story format에 따라 달라지므로 Twine의 '단일 조건 문법'이 있다고 간주하지 않는다.

**결론**: 가장 먼저 만들 것은 3D 그래프 UI도, 범용 스크립트도 아니다. **validator + 조건의 실행 의미 + 플레이 테스트 패널**이다.
