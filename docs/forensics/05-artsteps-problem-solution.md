# Artsteps / Metasteps — 3D 공간을 관람 경험으로 바꾸는 방식

검토일: 2026-11-08  
근거 수준: 공식 제품 설명/Artsteps 공식 블로그(B), 구조적 해석(C)

> 공개 source가 없기 때문에 내부 코드·DB·3D engine은 단정하지 않는다.

# 1. Artsteps가 푸는 핵심 문제

일반 3D engine은:
- mesh
- camera
- light
- material
를 제공하지만 전시 제작자에게는 너무 기술적이다.

전시 제작자가 원하는 것은:
- 공간 만들기
- 작품 올리기
- 위치 정하기
- 설명하기
- 관람시키기
- 공유하기

Artsteps의 핵심은 **3D 기술 용어를 전시 제작 workflow로 번역**한 데 있다.

# 2. 기술 객체보다 도메인 객체를 앞세운다

사용자가 다루는 개념:

```text
Space
Artifact
Placement
Narrative / Guide
Exhibition
Visitor
Share
```

3D engine의 scene graph를 그대로 노출하지 않는다.

## 설계 교훈

전문 기술을 초보자 서비스로 바꿀 때:
- engine primitive
가 아니라
- 사용자의 domain primitive
를 UI의 중심으로 둔다.

# 3. 여러 media를 "Artifact"라는 공통 공간 단위로 묶는다

공식 설명:
- 2D paintings/photos/posters
- 3D objects/sculptures/installations
- streaming video

사용자 관점에서는 모두 **전시할 것**이다.

따라서 제품 개념상:

```text
Artifact
├─ 2D
├─ 3D
└─ Video
```

로 통합된다.

## 장점

새 media type이 추가되어도:
- 공간 배치
- 관람
- 설명
이라는 상위 workflow는 유지된다.

# 4. 제작과 관람을 하나의 lifecycle로 연결

```text
Design
→ Create
→ Explore
→ Participate
→ Share
```

공식 Artsteps가 이 흐름을 직접 slogan으로 사용한다.

즉 authoring tool에서 끝나는 것이 아니라:
- viewer
- social
- publication
까지 제품 범위다.

# 5. "자유 이동만 있으면 관람된다"는 문제를 Guide로 푼다

3D 자유 공간의 가장 큰 UX 문제:
- 어디로 가야 하는지 모름
- 작품을 놓침
- 의도한 순서가 무너짐

Artsteps의 storytelling/guide 개념은 **공간에 관람 서사를 추가**한다.

논리적으로:

```text
Spatial location
   +
Narrative order
   +
Artwork context
```

를 연결한다.

이 부분이 놀아트에 가장 직접적으로 유용하다.

# 6. Visitor를 단순 카메라가 아니라 social actor로 본다

공식 자료:
- registered users appear as avatars
- chat
- comments
- rating/discussion

즉 viewer는 passive renderer가 아니다.

```text
Visitor
├─ identity/avatar
├─ navigation
├─ communication
└─ feedback
```

# 7. Publication을 제품의 핵심 단계로 둔다

전시 결과는:
- Artsteps에서 탐색
- social channel 공유
- website embed
- private/custom space
로 배포할 수 있다.

이것은 "3D editor에서 저장"보다 **관람 가능한 publication artifact를 만든다**는 관점이다.

# 8. Private Space는 exhibition과 membership을 분리하는 방향을 보여준다

2020 공식 사례:
- custom domain 형태
- registration code
- member visibility
- public publication permission

즉 product domain은 단순 scene 외에:
- organization/space
- member
- access
- exhibition
개념을 갖는 방향으로 확장된다.

정확한 내부 schema는 Unknown.

# 9. Metasteps는 단일 전시를 "공간 시스템"으로 확장

2025 공식 Artsteps의 Metasteps 소개에서 공개된 기능:

- Portals
- Info Points
- Guided Paths
- Webviews
- member-only access
- role-based permissions
- Districts

## Portal

```text
Space A ↔ Space B ↔ Space C
```

한 전시의 방이 아니라 여러 환경을 연결하는 **space graph**.

## Info Point

3D object/location에 설명과 interaction을 연결.

## Multiple Guided Paths

같은 공간에서 관람 목적별 경로를 다르게 만들 수 있다.

교육적으로:
- 기본 코스
- 심화 코스
- 주제별 코스
같은 구조가 가능하다.

## Webviews

3D 안에:
- form
- survey
- presentation
- external collaboration content
를 포함할 수 있다.

즉 공간이 단순 media gallery에서 **application container**로 확장된다.

# 10. 권한도 content 수준에서 공간/조직 수준으로 확장

Metasteps:
- member-only
- role-based permissions
- Districts

제품 진화 방향을 논리적으로 보면:

```text
Content
→ Exhibition
→ Space
→ Connected Spaces
→ Organization Hub
→ Membership / Role
```

# 11. Artsteps 방식의 장점

1. 3D 기술을 전시 도메인 언어로 바꾼다.
2. heterogeneous media를 artifact라는 공통 개념으로 묶는다.
3. 제작→관람→공유 lifecycle이 끊기지 않는다.
4. guide로 자유 3D 공간의 방향 상실을 완화한다.
5. social presence가 "같이 관람" 경험을 만든다.
6. Metasteps는 multi-space와 guided path로 확장성을 높인다.

# 12. 구조적 한계 / 비용

1. 3D는 모바일·저사양 기기 비용이 높다.
2. 자유 이동은 초보자에게 어렵다.
3. 너무 큰 공간은 길찾기 자체가 과제가 된다.
4. 3D authoring은 2D gallery보다 제작 시간이 길다.
5. hosted platform 의존 시 portability가 중요해진다.
6. 내부 기술이 비공개라 implementation pattern을 직접 재사용할 수 없다.

# 13. 놀아트에 실제로 가져올 부분

가장 가치 높은 것은 character/graphics가 아니다.

```text
Artwork
  ↓
Placement

Artwork
  ↓
Viewpoint

Viewpoints
  ↓
Guided Path

Spaces
  ↓
Portal/Transition
```

그리고 반드시:

```text
3D Guided View
+
Free Roam
+
2D Accessible Fallback
```

을 함께 생각하는 것이 적합하다.

# 14. 확인할 수 없는 것

- engine
- renderer
- scene serialization
- autosave implementation
- database
- object storage
- realtime protocol
- collision/navmesh
- camera interpolation
- backend language

이 영역은 Unknown으로 유지한다.
