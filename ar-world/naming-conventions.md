# Naming Conventions

## C# 코드
| 대상 | 규칙 | 예시 |
|---|---|---|
| 클래스 | PascalCase | `AnchorService`, `MemoObject` |
| 인터페이스 | `I` + PascalCase | `IAnchorService` |
| 메서드 | PascalCase | `PlaceObject()`, `RestoreAnchorsAsync()` |
| 프로퍼티 | PascalCase | `IsTracking`, `AnchorCount` |
| private 필드 | `_camelCase` | `_anchorService`, `_arCamera` |
| 로컬 변수/파라미터 | camelCase | `anchorData`, `hitPose` |
| 상수 | `UPPER_SNAKE_CASE` | `ANCHOR_FILE_NAME`, `MAX_OBJECTS` |
| 이벤트 | `On` + PascalCase | `OnAnchorPlaced`, `OnObjectSelected` |
| async 메서드 | `~Async` 접미사 | `LoadAnchorsAsync()` |

## Unity 에셋
| 대상 | 규칙 | 예시 |
|---|---|---|
| 씬 | PascalCase | `MainAR.unity`, `Menu.unity` |
| 프리팹 | PascalCase (스크립트명과 일치) | `MemoObject.prefab` |
| 스크립트 파일 | 클래스명과 동일 | `AnchorService.cs` |
| ScriptableObject | PascalCase + `_Config` 또는 `_Data` | `ARObject_Config.asset` |
| 머티리얼 | 용도_색상 | `Memo_White.mat` |
| 텍스처 | 용도_T (혹은 _N, _M) | `Memo_T.png` |

## 폴더 및 네임스페이스
- 폴더: `PascalCase` (Unity 관례)
- 네임스페이스: `ARWorld.{레이어}.{도메인}` 형식
  - `ARWorld.Services`
  - `ARWorld.AR.Managers`
  - `ARWorld.UI.Views`
