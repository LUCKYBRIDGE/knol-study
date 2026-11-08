# H5P — 얽힌 문제 해결

| 문제 | 해법 |
|---|---|
| LMS마다 DB/API 다름 | Framework Interface |
| file backend 다름 | H5PFileStorage |
| activity 종류 계속 증가 | versioned Content Library |
| 매번 editor form 재작성 | semantics-driven editor |
| 특수 authoring UI 필요 | custom editor dependency |
| runtime에 editor code까지 포함 | dependency 종류 분리 |
| 콘텐츠 이동 | .h5p package |
| 오래된 content | library upgrades |
| malicious upload | package/file/content validation |
| 학습 결과 통합 | event/xAPI contract |
| 이어하기 편차 | content type state contract + host |

## 중요한 trade-off

확장성이 커질수록:
- dependency graph
- version matrix
- migration
- security permission
비용이 커진다.

Knol에서는 활동 타입이 실제로 반복된 뒤 registry를 추출하는 편이 맞다.
