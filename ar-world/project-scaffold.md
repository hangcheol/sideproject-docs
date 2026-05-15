# Project Scaffold

## 디렉토리 구조

```
secondBusiness/
  ar-world/                         ← Unity 프로젝트 루트
    Assets/
      _Project/                     ← 언더스코어로 상단 정렬 (Unity 에셋보다 위)
        Scenes/
          MainAR.unity
          Menu.unity
        Scripts/
          Core/
            Services/               ← 순수 C# 서비스
              AnchorService.cs
              PersistenceService.cs
            Models/                 ← 데이터 모델 (직렬화 대상)
              AnchorData.cs
              ARObjectData.cs
          AR/
            Managers/               ← AR Foundation 연동 MonoBehaviour
              ARSessionManager.cs
              ARObjectSpawner.cs
              ARObjectRegistry.cs
          UI/
            Views/                  ← UI MonoBehaviour
              MainMenuView.cs
              ObjectPlacementView.cs
            Controllers/            ← UI 로직 (순수 C# 또는 MB)
        Prefabs/
          ARObjects/                ← AR 공간에 배치되는 오브젝트
            MemoObject.prefab
          UI/
        ScriptableObjects/
          Config/                   ← 앱 설정값 (스폰 거리, 크기 등)
      Plugins/                      ← 서드파티 SDK (Newtonsoft.Json 등)
    Packages/                       ← Unity Package Manager (자동 관리)
    ProjectSettings/                ← 프로젝트 설정 (Git 포함)
    UserSettings/                   ← 개인 에디터 설정 (Git 제외)

docs/
  ar-world/                         ← AR World 전용 가이드라인 (이 폴더)
```

## .gitignore 주요 항목 (Unity)
```
# 자동 생성 — Git 제외
[Ll]ibrary/
[Tt]emp/
[Oo]bj/
[Bb]uild/
[Bb]uilds/
[Ll]ogs/
UserSettings/
*.pidb.meta
*.pdb.meta

# 포함해야 할 것
Assets/            ← 반드시 포함
ProjectSettings/   ← 반드시 포함
Packages/          ← 반드시 포함
```

## 씬별 책임
| 씬 | 역할 |
|---|---|
| `Menu.unity` | 앱 진입점. 설정, 오브젝트 목록 선택 |
| `MainAR.unity` | 핵심 AR 씬. 카메라 피드 + 오브젝트 배치/조작 |

## 데이터 저장 경로
- 앵커 데이터: `Application.persistentDataPath + "/anchors.json"`
- 앱 설정: `Application.persistentDataPath + "/settings.json"`
