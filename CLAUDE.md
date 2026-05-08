# 🏛️ Core Project Constitution

## 🌟 Overview
이 프로젝트는 Java 21 기반의 고성능 MSA 서비스입니다.
아키텍처는 **Hexagonal Architecture (포트 & 어댑터 패턴)**을 따릅니다.
모든 작업은 정의된 가이드를 최우선으로 따릅니다.

## 📍 Guideline Index (반드시 참고할 것)
- **기술 스택 및 버전:** `docs/guidelines/tech-stack.md`
- **Hexagonal 아키텍처:** `docs/guidelines/architecture.md`
- **Java/Spring 코딩 표준:** `docs/guidelines/coding-standards.md`
- **네이밍 규칙 (패키지·클래스·메서드):** `docs/guidelines/naming-conventions.md`
- **프로젝트 스캐폴딩 (폴더 구조·포트·표준 의존성):** `docs/guidelines/project-scaffold.md`
- **API 설계 규칙 (URL·응답 포맷·에러 코드):** `docs/guidelines/api-conventions.md`
- **Git 브랜치 및 커밋 전략:** `docs/guidelines/git-convention.md`
- **인프라 및 배포 전략:** `docs/guidelines/infrastructure.md`
- **AI 협업 및 작업 절차:** `docs/guidelines/ai-collaboration.md`
- **워크플로우 매뉴얼:** `docs/guidelines/workflow-manual.md`
- **팀원 역할 정의:** `docs/guidelines/roles.md`
- **[백업] 기존 DDD 4계층 규약:** `docs/guidelines/ddd-backup/`

## 🌶️ Hotplace Simulator 프로젝트 스펙
- **서비스 목록·포트·도메인 엔티티:** `docs/hotplace/service-spec.md`
- **Hotplace 기술 스택 (PostGIS, WebSocket, Redis):** `docs/hotplace/tech-stack.md`
- **Feign 의존관계 및 fallback 규약:** `docs/hotplace/feign-contracts.md`
- **작업 계획서 (Phase별 체크리스트):** `firstBusiness/hotplace/PLAN.md`
- **로컬 인프라 (docker-compose):** `firstBusiness/hotplace/docker-compose.yml`

## 🚀 Top 3 Golden Rules
1. **Plan First:** 코드를 쓰기 전 계획(Plan)을 제시하고 컨펌받을 것.
2. **Self-Correction:** 에러 발생 시 스스로 3회까지 해결 시도할 것.
3. **Command Driven:** `workflow-manual.md`의 [A], [B], [C] 매뉴얼을 준수할 것.