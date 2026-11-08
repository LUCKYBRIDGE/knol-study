# Padlet — 공개 구조와 Unknown

## 공개 source
확인되지 않음.

따라서 Unknown:
- frontend framework
- database
- realtime transport
- object storage
- conflict algorithm
- moderation backend
- asset pipeline

## 제품 수준 domain

```text
Account / Organization
├─ Board
│  ├─ Post
│  │  ├─ Content
│  │  ├─ Attachments
│  │  ├─ Comments
│  │  └─ Reactions
│  ├─ Layout
│  ├─ Activity
│  └─ Share/Permission
└─ Sandbox
   ├─ Cards
   ├─ Objects
   ├─ Links/Interactions
   └─ Card-level access
```

## 핵심 분석 포인트
Padlet 내부 코드 구조를 추측하는 대신:
- Board
- Post
- Layout
- Sandbox Card
- Permission
- Share
- Feedback
같은 사용자-facing contract를 분석한다.
