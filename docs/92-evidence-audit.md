# 심층 분석 검증 기록 — 2026-11-08

## 범위
- 완료: 다섯 서비스 공식 설명/개발 문서 검토, tldraw·H5P·Twine·Excalidraw 공식 GitHub 소스의 핵심 구조 확인, 라이선스 경계, 시나리오 기반 테스트 계획.
- 완료: `LUCKYBRIDGE/knolart`, `knolprint`, `new-knolstory`, `knolhistory`의 현재 main과 코드/설계 수준 Fit–Gap 대조.
- 미완료: Craft Anything CLI 자체 실행, 대상 사이트의 브라우저 실제 조작 녹화, 네트워크 trace, 성능 프로파일링, 실사용자 과제 테스트.
- 따라서 모든 결과를 **역공학으로 내부 구현을 확증한 것**으로 표현하면 안 된다.

## 1차 문서에서 주의할 해석
1. **tldraw**: document/session 외 presence scope도 중요하다. 공식 Store 문서로 확인했다. 로컬 저장과 서버 공유는 구분.
2. **H5P**: 모든 콘텐츠가 자동으로 이어하기를 지원하는 것이 아니다. 콘텐츠 타입별 state API 및 통합 설정이 필요. H5P 모든 코드의 라이선스가 MIT인 것도 아님.
3. **Twine**: 루프는 오류가 아니라 의도된 그래프일 수 있음. 정적 reachability와 실제 조건 충족 가능한 reachability는 다름.
4. **Artsteps**: guide point 존재는 공식 확인됐지만 자유/안내 모드 상세 UI, 카메라 보간, 모바일 실제 조작감은 관찰 전까지 단정하지 않는다.
5. **Excalidraw**: npm 에디터 패키지와 excalidraw.com 호스팅 앱의 오프라인/PWA/협업/E2EE 기능을 혼동하지 않는다. ‘7~9개 도구’는 제품 규칙이 아닌 실험 가설이다.

## 검증 가능한 근거
- tldraw store: https://tldraw.dev/sdk-features/store
- tldraw persistence: https://tldraw.dev/sdk-features/persistence
- tldraw sync: https://tldraw.dev/docs/sync
- tldraw license: https://tldraw.dev/community/license
- H5P specification: https://h5p.org/documentation/developers/h5p-specification
- H5P semantics: https://h5p.org/semantics
- H5P branching scenario: https://h5p.org/branching-scenario
- H5P state restoration discussion: https://h5p.org/comment/51583
- H5P licensing: https://h5p.org/licensing
- Twine story map: https://twinery.org/reference/en/editing-stories/navigating.html
- Twine linking: https://twinery.org/reference/en/editing-stories/linking-passages.html
- Twine export: https://www.twinery.org/reference/en/story-library/exporting.html
- Artsteps official: https://www.artsteps.com/
- Excalidraw repository README: https://github.com/excalidraw/excalidraw/blob/master/README.md

## 다음 단계에서 실제로 수집할 증거
각 도구의 PC 1440px, 태블릿 약 768px, 모바일 390px 폭에서:
- 진입 전/후 화면 캡처
- 과제별 사용자 행동 및 메뉴 상태
- 저장/복구/내보내기 실패 및 성공
- 각 환경의 브라우저/버전
- 테스트 시각과 실제 동작 증거
- 확인 불가 항목과 그 이유

저장 위치 제안: `evidence/<tool>/<date>/`. 캡처 파일은 제3자 콘텐츠의 저작권·개인정보·접근 권한을 확인한 뒤 보관.

## 중요한 실행 원칙
기존 서비스 저장소의 현재 소스를 검사하지 않고 '이미 구현됨' 또는 '수정 가능'으로 결론 내리지 않는다. 실제 리팩터링 전에 각 저장소의 main 구조 및 현재 저장 포맷을 조사하고 데이터 마이그레이션 위험을 먼저 적는다.


## 2026-11-08 소스 수준 대조에서 새로 확인한 사실

- tldraw 저장소가 state/store/editor/tlschema/sync-core/sync 등을 패키지 단위로 분리함을 확인.
- Excalidraw `LocalData.ts`에서 elements/AppState와 BinaryFiles 저장소를 분리하고 debounce, quota 처리, collaboration save lock을 확인.
- Excalidraw `APP_STATE_STORAGE_CONF`에서 각 AppState 필드의 browser/export/server 포함 여부를 별도 선언하는 구조 확인.
- H5P MultiChoice/InteractiveVideo의 실제 `library.json`과 `semantics.json`에서 version/dependency/editor dependency/widget/importance/showWhen 등을 확인.
- Twine 코드의 stories/persistence/undoable-stories/story-formats 모듈 분리를 확인.
- 놀아트의 project validation, IndexedDB recovery, history budget은 이미 구현됨.
- 놀프린트는 의도적으로 원본을 메모리에만 유지하며 설정만 localStorage에 보관함.
- 놀스토리는 StoryDocument v8, flow, score condition/effect/route, branch condition, runtime separation을 이미 구현함.
- 놀히스토리는 아직 핵심 경험을 고정하지 않은 Foundation 단계임.

따라서 초기 문서와 충돌하는 경우 `docs/systematic/` 분석을 우선한다.
