# MakeCode / PXT — 얽힌 문제 해결

| 문제 | 해결 |
|---|---|
| blocks와 text API가 달라짐 | 하나의 TypeScript API + annotation |
| block UI가 복잡해짐 | advanced/group/expandable metadata |
| 학생이 hardware 없으면 못 배움 | simulator first |
| device마다 editor 재개발 | Target abstraction |
| 새 sensor/device block | Extension package |
| extension이 editor DOM 침범 | iframe editor extension |
| blocks↔text round-trip 깨짐 | 제한된 TypeScript subset |
| hardware 연결 friction | WebUSB/download options |
| domain마다 specialized input 필요 | field editor registry |
| 초보→고급 전환 | Blocks → text → debugger/GitHub |

## Knol에 유용
- 쉬운 UI와 고급 표현을 같은 semantic model에 연결
- progressive disclosure를 metadata로 선언
- simulator-first 학습
- target/plugin whitelist
