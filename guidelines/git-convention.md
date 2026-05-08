# 🌿 Git Branching & Commit Strategy

## 1. Branch Strategy
- **`main`:** 상용 배포용 브랜치.
- **`develop`:** 통합 개발 및 스테이징 브랜치.
- **`feature/*`:** 기능 단위 개발 브랜치. (예: `feature/order-api`)

## 2. Workflow with AI
1. 클로드는 작업을 시작하기 전 현재 브랜치를 확인하고, 필요시 새로운 `feature` 브랜치를 생성한다.
2. **[C] 검증 단계**를 통과하면 변경 사항을 커밋한다.
3. 커밋 메시지는 `feat: `, `fix: `, `refactor: ` 등의 컨벤션을 지킨다.

## 3. Deployment Flow
`feature` → (PR/Review) → `develop` → (Validation) → `main`