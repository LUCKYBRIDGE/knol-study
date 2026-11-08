# 현재 의사결정 매트릭스

검토일: 2026-11-08

| 외부 패턴 | 놀아트 | 놀프린트 | 놀스토리 | 놀히스토리 | 판단 |
|---|---|---|---|---|---|
| tldraw document/session/presence | ADAPT | ADAPT 일부 | ALREADY 유사 경계 | DEFER | lifecycle 원리는 채택 |
| tldraw SDK 직접 도입 | REJECT 현재 | REJECT 현재 | REJECT | REJECT | 라이선스·구조 불일치 |
| Excalidraw browser/export/server state policy | ADOPT | ADOPT | 참고 | 향후 | 높은 가치 |
| Excalidraw local binary store | ALREADY 유사 | ADAPT opt-in | 별도 archive 있음 | DEFER | 목적별 적용 |
| H5P type manifest | 낮음 | 낮음 | DEFER | DEFER | 놀퀴즈 3종 spike가 우선 |
| H5P generic semantics UI | REJECT 전면 | REJECT 전면 | REJECT 전면 | ADAPT 일부 | 전용 UX 유지 |
| Twine Story Map | 낮음 | - | ADOPT | 조건부 ADAPT | 놀스토리 최우선 |
| Twine variable/condition engine | - | - | ALREADY | DEFER | 새로 만들지 않음 |
| Twine story format 생태계 | - | - | REJECT | REJECT | 현재 Runtime Core 유지 |
| Artsteps guide point | ADOPT | - | - | 시나리오에 따라 | viewpoint/tour로 변형 |
| 3D 캐릭터 고도화 | DEFER | - | - | - | guided UX 이후 |
| full autosave | 이미 recovery 있음 | REJECT 기본 | ALREADY local-first | DEFER | 서비스별 요구 다름 |
| opt-in recovery | ALREADY 더 강함 | ADOPT 후보 | 불필요 | 향후 | privacy 고려 |
| 공통 persistence 패키지 | DEFER | DEFER | DEFER | DEFER | premature abstraction 금지 |

## 즉시 개발로 이어질 후보

1. **놀아트 viewpoint/guided route**
2. **놀스토리 읽기 전용 Story Map**
3. **놀스토리 cycle 정책 fixture**
4. **놀아트/놀프린트 storage destination policy audit**
5. **놀프린트 opt-in recovery 사용자 가치 검증**

## 일부러 지금 하지 않을 것

- tldraw 전체 SDK 이식
- Twine 엔진 재구축
- 놀스토리 score/condition 신규 개발
- 놀아트 recovery 재구축
- 놀히스토리 범용 activity/branching framework 선행 구현
- 여러 서비스 공용 패키지를 증거 없이 먼저 만드는 것

이 문서는 외부 제품을 얼마나 많이 닮을지를 결정하는 표가 아니라, **현재 Knol 구조를 보존하면서 실제 결손만 채우기 위한 의사결정 기록**이다.
