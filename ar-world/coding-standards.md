# C# Coding Standards (Unity)

## 필드 선언
```csharp
// 인스펙터 노출이 필요한 경우 — public 대신 이것 사용
[SerializeField] private ARObjectSpawner _spawner;

// 외부에서 읽기만 허용할 때
public ARObjectData Data { get; private set; }

// 상수
private const string ANCHOR_FILE_NAME = "anchors.json";
```

## 비동기
```csharp
// Unity Coroutine 대신 async/await 사용
public async Task RestoreAnchorsAsync()
{
    var data = await _persistenceService.LoadAsync();
    foreach (var anchor in data) { ... }
}

// UniTask 도입 후엔 Task → UniTask로 교체
```

## AR 성능 규칙
- `FindObjectOfType<T>()` — **Start()에서만** 허용, Update() 절대 금지
- Raycast는 터치 이벤트 발생 시에만. 매 프레임 Raycast 금지
- AR Plane/Anchor 이벤트는 콜백(`trackablesChanged`) 방식으로 처리

## MonoBehaviour 작성 규칙
```csharp
public class ARObjectSpawner : MonoBehaviour
{
    [SerializeField] private GameObject _arObjectPrefab;

    private AnchorService _anchorService;   // 직접 new 금지, 외부 주입

    private void Start()
    {
        _anchorService = FindObjectOfType<AnchorService>(); // Start에서만 허용
    }

    // Update에는 최소한의 로직만
    private void Update() { }

    // 실제 로직은 private 메서드로 분리
    private void PlaceObject(Pose pose) { ... }
}
```

## Service 클래스 작성 규칙
```csharp
// MonoBehaviour 상속 금지
public class AnchorService
{
    private readonly PersistenceService _persistence;

    public AnchorService(PersistenceService persistence)
    {
        _persistence = persistence;
    }

    public async Task<List<AnchorData>> LoadAnchorsAsync() { ... }
}
```

## 에러 처리
- AR 트래킹 불가 상태는 UI로 사용자에게 안내 (예외 무시 금지)
- `try/catch`는 파일 I/O, 외부 API 호출에만 사용
