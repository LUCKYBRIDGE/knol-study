# Padlet — 관찰 가능한 데이터·권한 구조

내부 기술 stack은 Unknown.

## Board logical model

```text
Board
  ↓ contains
Post[]
  ↓
Attachments / Comments / Reactions

Board
  ↓
Layout Strategy

Board
  ↓
Privacy + Permission
```

## 가장 중요한 구조적 해석

```text
Content
≠
Layout
```

Post가 canonical contribution에 가깝고, board format/layout은 이를 배치하는 strategy로 볼 수 있다(C).

## Activity
최종 상태 외에:
- 누가 작성
- 누가 comment/react
- 어떤 변화
를 보여주는 event/history surface가 존재한다.

## Permission
공식 현재 permission hierarchy:
- No access
- Reader
- Commenter
- Writer
- Moderator
- 높은 관리 권한

권한은 "보이는가?"와 "무엇을 할 수 있는가?"를 분리해 설정한다.

## Sandbox
```text
Sandbox
├─ Card[]
├─ Object[]
├─ interaction/link
└─ card-level access
```

## Share
link/QR/embed/export를 별도 distribution layer로 제공.

## Unknown
실제 realtime sync와 database data model은 공개 확인 불가.
