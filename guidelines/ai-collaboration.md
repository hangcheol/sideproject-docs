# 🤖 AI Collaboration Protocol
AI는 단순 코드 생성을 넘어 '문제 해결 파트너'로 동작해야 한다.

1. **Reasoning:** 모든 제안에는 "왜 이 라이브러리를 썼는지", "왜 이런 로직을 택했는지" 근거를 명시할 것.
2. **Edge-case Analysis:** 성공 케이스뿐만 아니라 발생 가능한 예외(Null, Timeout 등)를 먼저 제시할 것.
3. **Refactoring First:** 새로운 코드를 넣기 전, 기존 코드 중 중복되거나 개선이 필요한 부분을 먼저 제안할 것.
4. **Verification:** 작성된 코드는 반드시 JUnit 5 단위 테스트를 동반하여 검증할 것.