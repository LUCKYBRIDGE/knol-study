# Excalidraw — 얽힌 문제 해결

| 문제 | 해결 |
|---|---|
| 버튼/shortcut/API 로직 불일치 | Action registry |
| UI 상태가 작품 파일에 누출 | APP_STATE_STORAGE_CONF |
| drag 중간 변화가 undo 오염 | durable/ephemeral capture |
| undo가 협업 version을 과거로 되돌림 | delta를 새 변화로 적용 |
| JSON에 이미지가 섞여 비대해짐 | BinaryFiles/FileManager |
| reload | localStorage + IDB |
| realtime 전달과 저장 혼동 | Socket / Firebase 분리 |
| 여러 client order 충돌 | reconcile + version + fractional index |
| 복잡한 기능 증가 | command palette + contextual UI |
| collaboration 회귀 | convergence tests |

## Knol에 유용

- 저장 목적지 matrix
- change durability
- Binary asset manager
- 같은 기능의 명령 통합
- realtime ≠ durable
- convergence test
