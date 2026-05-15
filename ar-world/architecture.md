# Architecture

## 레이어 구조

```
[ View Layer ]          MonoBehaviour — UI 표시, 터치 이벤트 수신
      ↕
[ Manager Layer ]       MonoBehaviour — AR 시스템과 Service를 연결
      ↕
[ Service Layer ]       Pure C# — 핵심 비즈니스 로직 (테스트 가능)
      ↕
[ Data Layer ]          Model(C# class) + Persistence(JSON 파일)
```

### 각 레이어 책임
- **View:** 화면에 보이는 것만. 로직 없음. Manager에 이벤트 전달.
- **Manager:** Unity 라이프사이클(Start/Update) 처리. Service 호출. AR Foundation API 직접 사용.
- **Service:** 앵커 관리, 오브젝트 생성/삭제/복원 등 핵심 로직. MonoBehaviour 의존성 없음.
- **Data:** 앵커 위치·회전·타입·메타데이터를 JSON으로 `persistentDataPath`에 저장.

## 핵심 컴포넌트

```
ARSessionManager          — AR 세션 시작/종료, 트래킹 상태 관리
ARObjectSpawner           — 화면 터치 → Raycast → 오브젝트 배치
AnchorService             — ARAnchor 생성/파괴 + JSON 저장/불러오기
ARObjectRegistry          — 씬에 존재하는 모든 AR 오브젝트 목록 관리
PersistenceService        — 파일 읽기/쓰기 (AnchorService가 사용)
```

## 앵커 영속성 전략 (MVP)

```
앱 종료 전:
  AnchorService → PersistenceService.Save(List<AnchorData>)
  → Application.persistentDataPath/anchors.json

앱 재실행 시:
  PersistenceService.Load() → List<AnchorData>
  → AnchorService.RestoreAnchors()
  → AR 공간 인식 후 해당 위치에 오브젝트 재배치
```

> **한계:** 로컬 저장이라 같은 기기+같은 공간에서만 복원됨. 기기 간 공유는 Cloud Anchors(MVP 이후) 단계에서.

## 씬 구성

```
MainAR.unity
  ARSession             (AR Foundation 기본 오브젝트)
  ARSessionOrigin       (카메라 + 트래킹 원점)
    AR Camera
  Managers              (빈 GameObject — 모든 Manager 컴포넌트 부착)
  UI Canvas             (월드 스페이스 또는 Screen Space Overlay)
```
