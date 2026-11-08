# H5P — 콘텐츠 엔진 심층 검토

검토일: 2026-11-08. **공식 규격 및 도움말 확인(A), 여러 H5P 호스팅 환경 간 실제 호환 실험은 미실시.**

## 1. 콘텐츠 구조에서 핵심은 '타입 계약'
H5P는 각 라이브러리의 `semantics.json`을 통해 입력 데이터를 선언하고, 범용 저작 폼 생성 및 실행 전 데이터 검증에 활용한다. [.h5p 명세](https://h5p.org/documentation/developers/h5p-specification)는 확장자가 ZIP 패키지이며 루트 `h5p.json`, 선택적 `content/content.json`, 의존성 라이브러리와 미디어 등을 규정한다.

**흐름**: 활동 유형 선택 → 스키마 기반 입력 + 맞춤 widget → 콘텐츠 JSON → 타입별 런타임 → 응답/완료/점수 → 재사용 또는 패키지 export.

### 중요: .h5p = 매번 완전 독립 실행 파일이 아니다
패키지는 호스팅 환경이 해석하고 필요한 라이브러리와 통합한다. 단순히 ZIP을 풀어 index.html을 열면 H5P 플레이어가 실행되는 구조라고 이해하면 안 된다. 의존 라이브러리 버전·호스팅 환경 지원이 호환성 변수가 된다.

## 2. 구성 모델
```text
ContentTypeRegistry
  ├── typeId / version / dependency constraints
  ├── schema (입력 필드/유효성)
  ├── authoring UI (기본 폼 + 맞춤 편집 컴포넌트)
  ├── runtime player
  ├── events / scores / state
  ├── migration / compatibility
  └── assets
ContentInstance
  ├── typeRef
  ├── metadata
  ├── content
  └── assetRefs
```

## 3. 사용성이 갈리는 구간
- **좋은 점**: 활동 종류 먼저 선택; 제작 단계에서 타입별 선택지만 노출; 이미 만든 콘텐츠의 다운로드·업로드.
- **비용**: 많은 타입이 선택 피로를 만든다. schema form이 자동 생성된다고 초등 교사가 쉽게 이해하는 것은 아니다.
- **교실 리스크**: 유형이 많을수록 점수 산정 의미가 달라질 수 있다. Branching Scenario는 경로별/최종 결말별 득점 방식이 다르며, [공식 소개](https://h5p.org/branching-scenario)에 구분이 있다.
- **학습 분석**: xAPI 같은 이벤트 표현을 참고하되 정답률을 학습 성취로 곧바로 해석하지 않는다.
- **이어하기**: H5P 상태 복구는 콘텐츠 타입이 `getCurrentState` 등을 구현하고 플랫폼의 상태 저장 설정이 켜져야 하는 등 **조건부**다. [개발자 토론](https://h5p.org/comment/51583).

## 4. 놀퀴즈 적용 모델
기존 서비스 전체를 H5P 플랫폼으로 마이그레이션하기보다 기존 문항 두 종류를 다음 계약으로 통일하는 작은 실험부터 시작한다.
```ts
interface KnolActivityType<T> {
 id: string;
 version: number;
 defaultData(): T;
 validate(data: unknown): data is T;
 renderAuthor(data: T): unknown;
 renderPlayer(data: T, context: unknown): unknown;
 migrate(data: unknown, fromVersion: number): T;
}
```
위 코드는 **제안된 인터페이스**이며 H5P의 실제 API가 아니다.

### 교육과정 연결을 위한 메타데이터
- 학년군, 교과, 성취기준, 내용 요소(지식·이해 / 과정·기능 / 가치·태도)
- 활동 목표와 피드백 방식
- 난도·소요 시간은 **검증된 사용 데이터 없이는 수치화하지 않음**
- 접근성: 키보드 조작, 화면 읽기, 대체 텍스트
- 답안 수집 여부와 개인정보 취급 기준

## 5. 재사용을 두 단계로
1. 작품/활동 복제: 원본을 유지하며 제목·내용 수정.
2. 문항/블록 재사용: id 재발급과 자산 참조 복제를 구분해야 함. 원본 블록 수정이 복제본을 바꾸는 링크형인지, 독립 사본인지 사용자에게 명확히.

## 6. 모듈화의 검증 항목
| 문제 | 실험 | 성공 기준 |
|---|---|---|
| 버전 변경 | v1 콘텐츠를 v2 플레이어에 로드 | migration 또는 의미 있는 오류 |
| 타입 삭제 | 타입 플러그인 없을 때 열기 | '이 콘텐츠 유형을 지원하지 않음' 안내 |
| 자산 누락 | 이미지 파일 삭제 후 import | 누락 표시와 나머지 문항 보존 |
| 블록 복제 | 프로젝트 A→B 붙여넣기 | ID 충돌·원본 덮어쓰기 없음 |
| 오프라인 | 캐시된 콘텐츠 로드 | 로컬 재생 범위를 명확하게 표시 |
| 평가 | 분기 재진입 반복 | 점수 중복 누적 정책 일관 |
| 접근성 | 태블릿/키보드/스크린리더 | 핵심 흐름 수행 가능 |

## 7. 라이선스
[H5P 공식 라이선스](https://h5p.org/licensing)는 여러 라이브러리에 MIT를 지향하지만 일부 구성 요소/서드파티 코드에는 GPL이 포함됨을 설명한다. **H5P 전체가 MIT라고 일반화하지 않는다.** 콘텐츠 라이선스와 소프트웨어 코드 라이선스도 별도로 관리한다.

## 8. 결론
핵심 차용 대상은 H5P 파일 그대로가 아니라 **type registry + 버전 있는 schema + 별도 player + 세분화된 재사용 단위**다. 놀퀴즈에서 2~3개 타입을 먼저 공통 계약으로 통합하고 실익을 검증한다.
