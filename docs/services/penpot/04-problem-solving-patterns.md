# Penpot — 얽힌 문제 해결

| 문제 | 해결 |
|---|---|
| 디자인과 실제 웹 layout 괴리 | CSS Flex/Grid 개념 채택 |
| 원본 component 변경과 instance customization 충돌 | sync group + touched override |
| 서로 다른 object 다중 편집 | mixed `:multiple` state |
| 오래된 design file | optional defaults + migration |
| export 결과와 화면 불일치 | 같은 shape renderer 최대 활용 |
| SVG에 없는 내부 정보 | metadata 추가 |
| designer→developer handoff | Inspect + HTML/CSS/SVG generation |
| realtime multi-backend | WebSocket + Redis pub/sub |
| heavy export | 별도 exporter/headless browser |
| extension | API/plugin/MCP 경계 |

## Knol에 유용
- 원본/사본 override
- mixed multi-selection UI
- default-by-absence schema
- render/export path 공유
- domain data와 code output 사이 직접 mapping
