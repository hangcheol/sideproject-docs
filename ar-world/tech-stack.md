# Tech Stack

## 핵심
- **Engine:** Unity 6000.0.x LTS
- **Language:** C# 10+ (.NET Standard 2.1)
- **AR Framework:** AR Foundation 6.x
  - iOS: ARKit XR Plugin 6.x
  - Android: ARCore XR Plugin 6.x
- **Input:** Unity Input System 1.x (New Input System, 구 Input 사용 금지)

## 패키지 (Package Manager)
| 패키지 | 용도 |
|---|---|
| `com.unity.xr.arfoundation` | AR 공통 API |
| `com.unity.xr.arkit` | iOS ARKit 백엔드 |
| `com.unity.xr.arcore` | Android ARCore 백엔드 |
| `com.unity.inputsystem` | 터치/제스처 입력 |
| `com.unity.nuget.newtonsoft-json` | 앵커 데이터 JSON 직렬화 |

## 향후 추가 후보 (MVP 이후)
- `ARCore Extensions` — Google Cloud Anchors (기기 간 앵커 공유)
- `VContainer` — 경량 DI 컨테이너 (서비스 규모 커지면 도입)
- `UniTask` — async/await 최적화 (Unity 기본 Task보다 가볍고 빠름)

## 빌드 타겟
- iOS 16.0+
- Android API Level 26+ (Android 8.0)
