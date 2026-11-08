# AFFiNE — 페이지·UI·UX·버튼

## Desktop top-level routes

실제 source:

- `/`
- `/workspace/:workspaceId/*`
- `/share/:workspaceId/:pageId`
- `/invite/:inviteId`
- `/onboarding`
- `/subscribe`
- `/theme-editor`
- `/clipper/import`
- `/template/import`
- `/template/preview`
- auth/sign-in/magic-link/oauth
- open-app
- upgrade 관련 route

## Workspace 내부 surface

실제 폴더 구조:
- All Pages
- Journals
- Collection
- Tags
- Attachments
- Chat
- Detail Page
- Settings
- Share
- Trash

## Detail/Page editor

핵심 UX:
- Page mode
- Edgeless mode
- block 기반 editing
- slash menu
- toolbar
- drag handle
- linked docs
- inline comment/reference
- remote selection
- zoom toolbar

## Edgeless toolbar

BlockSuite widget 구조에서 확인:
- edgeless-toolbar
- edgeless-zoom-toolbar
- selected-rect
- auto-connect
- dragging-area
- viewport overlay

즉 whiteboard mode가 별도 app이 아니라 같은 editor framework의 다른 surface다.

## Content modes

AFFiNE이 강조하는 경험:
- rich text
- sticky/note
- shapes/connectors
- linked page
- database/data view
- mindmap
- slides 등

같은 Workspace의 block/object를 다른 presentation으로 조직한다.

## Share/Collab UX
- remote selection
- comments
- document roles
- invite/share
- history permission
- workspace-level team membership

## Permission 예
실제 GraphQL permission:
- Doc_Update
- Doc_Users_Manage
- Doc_Users_Read
- Doc_Comments_Create
- Doc_Comments_Moderate
- Doc_Comments_Read
- Doc_History_Read

단순 viewer/editor 2단계보다 세밀하다.

## Mobile
desktop router와 별도 mobile router/pages를 가진다.

desktop를 CSS만 축소하는 것이 아니라 navigation/surface를 별도 구성한다.
