# 🕹️ Workflow Command Manual (Standard Operating Procedure)

클로드는 사용자의 단축 명령어에 따라 다음의 표준 작업 절차(SOP)를 엄격히 수행한다.

## 🟢 [A] Strategic Analysis & Planning (전략 및 기획)
- **Primary Role:** [Planner] + [Lead Architect]
- **Procedure:** 1. **Context Scan:** 관련 도메인의 `domain` 계층 코드를 먼저 읽고 비즈니스 규칙을 파악한다.
    2. **Impact Analysis:** 수정 시 영향을 받는 의존 관계를 식별한다.
    3. **Design Proposal:** `tech-stack.md`에 기반한 기술적 설계와 함께 `Plan`을 제시한다.
    4. **Reasoning:** 설계의 근거를 `ai-collaboration.md`에 따라 명시한다.

## 🔵 [B] Clean Implementation (구현 및 실행)
- **Primary Role:** [Senior Software Engineer]
- **Procedure:** 1. **Refactoring:** 기존 코드 중 개선 포인트를 먼저 제안한다.
    2. **Coding:** `coding-standards.md`를 준수하여 `record`, 생성자 주입 기반의 코드를 작성한다.
    3. **Optimization:** Java 21 가상 스레드 환경에 적합한 논블로킹(Non-blocking) 지향 코드를 구현한다.

## 🔴 [C] Comprehensive Validation (검증 및 마감)
- **Primary Role:** [QA Auditor]
- **Procedure:** 1. **Testing:** JUnit 5로 단위 테스트 코드를 작성하고 성공을 확인한다.
    2. **Self-Correction:** 에러 발생 시 로그를 분석하여 최대 3회 스스로 수정을 시도한다.
    3. **Review:** 엣지 케이스(Null, Timeout) 대응 여부를 최종 점검하고 보고한다.