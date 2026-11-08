# Twine — 얽힌 문제 해결

| 문제 | 해법 |
|---|---|
| graph가 초보자에게 어려움 | Passage + Story Map |
| node 먼저 만들고 link하는 작업 번거로움 | text link에서 passage 자동 생성 |
| broken structure 찾기 어려움 | inline visual diagnostics |
| map이 커져 위치 기억 불가 | fuzzy finder |
| 기본과 고급 문법 난이도 차이 | link-first + Story Format advanced |
| browser/desktop 저장 차이 | persistence adapter |
| 저작기와 실행기 결합 | Story Format publish boundary |
| import/export identity | IFID 분리 |
| extension이 core를 오염 | format-scoped extension |
| cycle을 오류로 오판 | self-loop를 정상 구조로 표현 |

## KnolStory에 특히 유용

- read-only Story Map
- broken/incomplete link 표시
- 조건/상태 debug overlay
- search/tag navigation
- local ID와 portable content ID 분리

Twine runtime 자체를 복제할 필요는 없다.
