# AR World Project Constitution

## Overview
Unity 6 기반의 크로스플랫폼(iOS/Android) AR 앱입니다.
실제 공간에 가상 오브젝트(메모, 피규어 등)를 배치하고, 앱을 껐다 켜도 **같은 위치에 유지**되는 것이 핵심입니다.
AR Foundation + ARKit/ARCore를 사용하며, 언어는 **C#** 입니다.

## Guideline Index
- **기술 스택 및 버전:** `docs/ar-world/tech-stack.md`
- **아키텍처:** `docs/ar-world/architecture.md`
- **C# 코딩 표준:** `docs/ar-world/coding-standards.md`
- **네이밍 규칙:** `docs/ar-world/naming-conventions.md`
- **프로젝트 폴더 구조:** `docs/ar-world/project-scaffold.md`

## Golden Rules
1. **Plan First:** 코드 작성 전 구조 설계를 먼저 제시하고 컨펌받을 것.
2. **No MonoBehaviour Logic:** 비즈니스 로직은 순수 C# 클래스(Service)에. MonoBehaviour는 연결·이벤트 처리만.
3. **AR은 비싸다:** 매 프레임 Raycast, FindObjectOfType 금지. 캐싱 또는 이벤트 기반으로.
