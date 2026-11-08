# tldraw — 페이지·UI·UX·버튼

## 1. 실제 tldraw.com routes

공식 `routeDefs.ts`:

| Route | 의미 |
|---|---|
| `/` | 로컬/홈 진입 |
| `/new` | 새 파일 |
| `/f/:fileSlug` | cloud file |
| `/f/:fileSlug/history` | 파일 history |
| `/f/:fileSlug/history/:timestamp` | 특정 history snapshot |
| `/lf` | local file 목록 |
| `/lf/:fileSlug` | local file |
| `/p/:fileSlug` | published view |
| `/import` | import |
| `/invite/:token` | 초대 |
| `/sso-callback` | SSO callback |
| `/oauth-consent` | OAuth |
| `/admin...` | admin |
| legacy `/r`, `/s`, `/ro`, `/v` | 과거 room/snapshot/read-only 호환 |

### UX적으로 중요한 점
local file과 cloud file을 URL 수준에서 구분한다.  
history도 별도 route로 분리해 현재 문서 편집과 과거 snapshot 탐색을 섞지 않는다.

## 2. Canvas 기본 도구

`useTools.tsx`에서 직접 확인:

- Select
- Hand
- Eraser
- Draw
- Rectangle / Ellipse 등 Geo shapes
- Arrow
- Line
- Frame
- Text
- Media
- Note
- Laser
- Embed
- Highlight 등

주요 shortcut도 tool 정의와 함께 보유한다.

예:
- Select: V
- Hand: H
- Eraser: E
- Draw: D/B/X
- Arrow: A
- Line: L
- Frame: F
- Text: T
- Note: N
- Laser: K

## 3. 주변 UI

### Main Menu
파일/편집/내보내기/환경 관련 동작.

### Toolbar
현재 제작 도구.

### Style Panel
선택한 객체/다음 객체의:
- 색
- fill
- dash
- size
- font
- alignment
등 context-sensitive 속성.

### Quick Actions / Actions Menu
자주 쓰는 command와 overflow action.

### Navigation Panel
zoom/navigation.

### Page Menu
다중 page 관리.

### Minimap
큰 canvas 탐색.

### Share Panel
공유/협업.

### Help / Keyboard Shortcuts / Accessibility
기능 학습과 접근성.

## 4. 반응형 Toolbar

`DefaultToolbar.tsx`:
- 가로/세로 orientation
- screen width에 따라 overflow
- 최근 사용 overflow tool을 main toolbar로 끌어올림
- tablet/mobile breakpoint에서 별도 style panel
- readonly에서는 editing controls 제거

### 풀어낸 문제
작은 화면에서 모든 도구를 그대로 축소하지 않고 **우선순위 + overflow**로 처리한다.

## 5. 도구 잠금

Toggle Tool Locked:
- 같은 도구를 반복 생성할 때 tool을 유지
- 한 번 만들고 Select로 돌아가는 기본 흐름과 구분

그리기/도형 반복 작업 효율을 높인다.

## 6. Readonly

tool별 `readonlyOk`가 존재한다.

예:
- Select
- Hand
- Laser
등 일부는 readonly에서도 허용.

즉 readonly를 화면 전체 disable 하나로 구현하지 않고 **행동별 가능 여부**를 둔다.

## 7. 중요한 UX 원리

- 도구와 shortcut 정의를 같은 registry에서 관리
- context-sensitive style
- mobile에서 overflow와 별도 panel
- editor와 view/history/publish를 route로 분리
- error reason별 사용자 메시지
- current tool을 명확한 state machine으로 관리
