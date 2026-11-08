# Artsteps — 외부에서 관찰 가능한 전체 구조도

검토 기준: 2026-11-08  
정확도: **중간(B/C)** — 공식 제품 설명·공식 블로그에서 확인 가능한 기능 기준. 내부 source/server/database는 비공개.

> Artsteps의 실제 내부 코드·DB·렌더링 엔진·서버 토폴로지는 공개 확인할 수 없다. 따라서 이 문서는 **제품 기능 구조는 B**, 내부 모듈 관계는 **C(추론)** 로 명확히 구분한다.

# 1. 공식적으로 확인되는 Product Model (B)

공식 설명에서 확인되는 주요 능력:

- browser-based 3D virtual exhibition authoring
- 3D room/space 구성
- 2D artifact: paintings/photos/posters
- 3D artifact: sculptures/installations
- streaming video
- audio/narration 가능
- exhibition navigation
- guide point / storytelling
- avatars / social interaction 관련 기능
- rating / discussion
- share / embed
- private/custom branded spaces 일부 제공
- desktop, smartphone, tablet, VR navigation 안내

# 2. Product Structure (B)

```mermaid
flowchart TB
  User[Creator]
  User --> Space[Build 3D Space]
  User --> Assets[Upload Digital Content]
  Assets --> Artifacts[Create Artifacts]
  Space --> Placement[Place Artifacts]
  Artifacts --> Placement
  Placement --> Guide[Guide Points / Narrative]
  Guide --> Publish[Publish Exhibition]
  Publish --> Viewer[Visitor Experience]
  Viewer --> Social[Rate / Discuss / Social]
  Publish --> Share[Share / Embed]
```

# 3. Authoring Structure (B/C)

```text
Exhibition
├─ Space
│  ├─ rooms
│  ├─ walls
│  ├─ floor
│  ├─ textures/colors
│  └─ environment
├─ Artifacts
│  ├─ image / painting / photo / poster
│  ├─ video
│  ├─ text/information
│  └─ 3D object
├─ Placement
│  ├─ position
│  ├─ rotation
│  └─ scale
├─ Narrative / Guide
│  └─ guide points / sequence
├─ Publication
│  ├─ visibility/access
│  ├─ share
│  └─ embed
└─ Social / Visitor
   ├─ avatar
   ├─ comments/discussion
   └─ rating
```

위 데이터 모델의 정확한 field/schema는 비공개이므로 개념 구조다.

# 4. Creation Flow (B)

공식 설명을 기반으로 한 사용자 흐름:

```mermaid
flowchart LR
  Start --> Space[Choose/build space]
  Space --> Upload[Upload content]
  Upload --> Place[Place artifacts]
  Place --> Describe[Add information/media]
  Describe --> Guide[Add guide/story points]
  Guide --> Preview[Preview]
  Preview --> Publish
  Publish --> Share[Share/embed]
```

# 5. Viewer Structure (B/C)

```mermaid
flowchart TB
  Enter[Enter exhibition]
  Enter --> Navigation[3D Navigation]
  Navigation --> Artifact[Approach/select artifact]
  Artifact --> Media[View image/video/3D/info]
  Navigation --> Guide[Follow narrative/guide]
  Guide --> Artifact
  Artifact --> Social[Comment/rate where enabled]
```

실제 카메라 interpolation, collision, navmesh, pointer lock, WebGL engine 등은 Unknown.

# 6. Guide Point System — 가장 중요한 개념

공식 설명에서 guide/storytelling이 확인된다.

내부 구현은 알 수 없지만 제품 구조상 다음 역할을 한다고 해석할 수 있다(C).

```text
Guide Point
├─ spatial target
├─ narrative order
└─ visitor guidance
```

이 개념의 핵심은:
- 공간 좌표
- 관람 순서
- 설명/서사
를 연결한다는 점이다.

# 7. Social / Identity Boundary (B)

공식 블로그 설명상 등록 사용자/아바타/communication/comments 등 social layer가 존재한다.

```mermaid
flowchart LR
  Account --> Avatar
  Account --> Creation
  Account --> Visit
  Visit --> Comment
  Visit --> Rating
  PrivateSpace --> Membership
```

정확한 auth provider, DB schema, moderation system은 Unknown.

# 8. Publishing / Embedding (B)

완성된 전시는:
- Artsteps 사이트에서 탐색
- social/digital channels 공유
- 다른 웹사이트에 embed
- 일부 조직용 custom/private space
방식으로 배포된다.

즉 authoring project와 viewer publication의 경계가 존재한다고 보는 것이 합리적이다.

# 9. 내부 기술 아키텍처 — 확인 불가

다음은 공개 근거가 없어 **Unknown**:
- frontend framework
- WebGL/3D engine
- scene graph format
- database
- object storage
- realtime protocol
- autosave implementation
- collision/navmesh
- camera controller internals
- cache/CDN
- backend language
- deployment topology

이를 임의로 Three.js/Unity/WebSocket 등으로 단정하지 않는다.

# 10. 추론 가능한 논리 계층 (C)

제품 기능만 놓고 보면 최소 다음 책임은 존재해야 한다.

```mermaid
flowchart TB
  AuthorUI[Authoring UI]
  SceneModel[Scene / Exhibition Model]
  AssetSystem[Asset System]
  Viewer3D[3D Viewer]
  GuideSystem[Guide / Narrative]
  PublishSystem[Publication]
  SocialSystem[Identity / Social]

  AuthorUI --> SceneModel
  AuthorUI --> AssetSystem
  SceneModel --> Viewer3D
  AssetSystem --> Viewer3D
  GuideSystem --> Viewer3D
  SceneModel --> PublishSystem
  AssetSystem --> PublishSystem
  PublishSystem --> Viewer3D
  SocialSystem --> AuthorUI
  SocialSystem --> Viewer3D
```

이것은 **필요 책임의 논리 구조**이지 Artsteps 내부 source tree가 아니다.

# 11. 장점

1. 3D 제작과 관람을 하나의 web product로 연결한다.
2. 다양한 artifact type을 동일 공간에 넣는다.
3. guide/narrative로 자유 공간에 관람 의도를 추가한다.
4. viewer/share/embed까지 creation lifecycle이 이어진다.
5. 교육·박물관·전시에 적합한 공간 메타포다.

# 12. 단점 / 비용

1. 3D 자체가 기기 성능과 입력 난도를 높인다.
2. 공간이 크면 길찾기 비용이 생긴다.
3. 자유 이동은 모바일/저숙련 사용자에게 부담이다.
4. 2D보다 제작 시간이 길 수 있다.
5. 내부 구조가 비공개라 기술 구현을 직접 참고하기 어렵다.
6. hosted service 의존 구조라 export/portability 범위는 별도 확인이 필요하다.

# 13. 놀아트에 가져올 구조

Artsteps의 내부 기술을 복제하기보다 다음 개념 구조를 가져오는 것이 적합하다.

```text
Exhibition
├─ Artwork
├─ Placement
├─ Viewpoint
├─ Tour
└─ Publication
```

특히:

```text
Artwork position
≠
Visitor viewpoint

Free movement
+
Guided route
+
2D fallback
```

가 핵심이다.

# 14. 공식 근거

- https://www.artsteps.com/
- https://blog.artsteps.com/about
- https://blog.artsteps.com/artsteps-make-your-own-virtual-exhibitions-1cbdc3a36146
- https://blog.artsteps.com/
