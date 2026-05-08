# 🏗️ Architecture Guidelines (Hexagonal / Ports & Adapters)

## 1. External Architecture (MSA)

- **Domain Isolation:** 각 핵심 도메인은 독립적인 프로젝트(Repository)로 관리한다.
- **Communication:** 서비스 간 통신은 `tech-stack.md`에 정의된 **OpenFeign**을 사용하며, 장애 전파 방지를 위해 **Resilience4j**를 필수로 적용한다.
- **Database per Service:** 각 마이크로서비스는 독립된 DB 스키마를 소유하며, 직접적인 크로스 DB 조인은 금지한다.

## 2. Internal Architecture (Hexagonal — Ports & Adapters)

의존성 방향: **adapter → port(interface) → domain** (단방향, 역방향 절대 금지)

```
com.{프로젝트명}.{domain}/
├── global/              ← 서비스 전역 공통 (ApiResponse, ErrorCode, Exception)
├── domain/              ← 순수 비즈니스 로직 (@Entity, @Service 등 프레임워크 어노테이션 금지)
├── port/
│   ├── in/              ← 입력 포트 (UseCase 인터페이스 — 외부에서 내부로의 계약)
│   └── out/             ← 출력 포트 (Repository 인터페이스 — 내부에서 외부로의 계약)
├── application/         ← UseCase 구현체 ({Domain}Service implements UseCase 인터페이스)
└── adapter/
    ├── in/
    │   └── web/         ← Web 어댑터 (Controller, Request/Response DTO)
    └── out/
        ├── persistence/ ← 영속성 어댑터 (JPA Entity, JPA Repository, RepositoryAdapter)
        └── external/    ← 외부 서비스 어댑터 (Feign Client, 외부 API 구현체)
```

### 2.1. domain/ — 순수 비즈니스 로직

- 프레임워크 어노테이션 (`@Entity`, `@Service`, `@Component` 등) **완전 금지**
- 외부 계층 import 금지 (JPA Entity, DTO, Spring 클래스 참조 금지)
- Java record 또는 일반 클래스로 비즈니스 상태와 행동만 표현

### 2.2. port/in/ — 입력 포트

- UseCase 인터페이스만 위치. 구현체는 `application/`에 둔다.
- 명명: `{Action}{Domain}UseCase` (예: `CreateUserUseCase`, `FindPlaceUseCase`)

### 2.3. port/out/ — 출력 포트

- 인프라 기술에 의존하지 않는 순수 인터페이스
- 명명: `{Domain}Repository` 또는 `{Domain}Port`
- domain 타입만 사용 (JPA Entity 타입 절대 사용 금지)

### 2.4. application/ — UseCase 구현체

- `{Domain}Service`가 `port/in/` 인터페이스들을 implements
- `port/out/` 인터페이스에만 의존 (JpaRepository 직접 주입 금지)
- 트랜잭션 경계는 여기서 관리

### 2.5. adapter/in/web/ — Web 어댑터

- Controller는 `port/in/` UseCase 인터페이스를 주입받는다 (Service 클래스 직접 주입 금지)
- Request/Response DTO는 Java record 사용
- 모든 컨트롤러 반환타입: `ResponseEntity<ApiResponse<T>>`

### 2.6. adapter/out/persistence/ — 영속성 어댑터

- `{Domain}JpaEntity`: `@Entity` 어노테이션은 여기에만 허용
- `{Domain}JpaRepository`: Spring Data JPA 인터페이스
- `{Domain}RepositoryAdapter`: `port/out/` 인터페이스를 implements, JpaEntity ↔ Domain 변환 담당

### 2.7. adapter/out/external/ — 외부 서비스 어댑터

- Feign Client 인터페이스 (`{TargetDomain}Client`)와 구현 어댑터
- 모든 Feign Client에 Resilience4j fallback 필수 적용

## 3. 핵심 의존성 규칙 요약

| 규칙 | 설명 |
|---|---|
| domain은 아무것도 import하지 않는다 | 외부 기술 완전 격리 |
| port는 domain 타입만 사용한다 | JPA Entity, DTO 금지 |
| application은 port/out 인터페이스에만 의존 | JpaRepository 직접 주입 금지 |
| Controller는 port/in UseCase에만 의존 | Service 클래스 직접 주입 금지 |
| @Entity는 adapter/out/persistence에만 | domain에 @Entity 금지 |

## 4. [백업] 기존 DDD 4계층 규약

이전 DDD(interfaces/application/domain/infrastructure) 방식은 `docs/guidelines/ddd-backup/`에 보관.
