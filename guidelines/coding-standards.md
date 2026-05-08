# 📏 Coding Standards
- **DTO/VO:** Java `record` 타입을 기본으로 사용.
- **Dependency Injection:** 필드 주입 엄금, 오직 생성자 주입만 사용.
- **Entity Management:** `@Setter` 사용 금지. 상태 변경은 의미 있는 도메인 메서드로 구현.
- **Conurrency:** 대량 트래픽 처리는 가상 스레드(`Virtual Threads`) 환경을 고려하여 작성.