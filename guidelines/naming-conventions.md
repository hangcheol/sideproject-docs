# 🏷️ Naming Conventions (Hexagonal Architecture)

## 1. 패키지 구조

Hexagonal Architecture(포트 & 어댑터 패턴)를 따른다.
의존성 방향: `adapter` → `port` → `domain` (단방향, 역방향 금지)

```
com.{프로젝트명}.{도메인}
  ├── global/            (서비스 전역 공통 — 응답 래퍼, 예외, 설정)
  ├── domain/            (순수 비즈니스 로직 — 외부 기술 의존 없음)
  ├── port/
  │   ├── in/            (입력 포트 — UseCase 인터페이스)
  │   └── out/           (출력 포트 — Repository 인터페이스)
  └── adapter/
      ├── in/
      │   └── web/       (Web 어댑터 — Controller, Request/Response DTO)
      └── out/
          ├── persistence/ (영속성 어댑터 — JPA Entity, JPA Repository 구현체)
          └── external/    (외부 서비스 어댑터 — Feign Client)
```

- `{프로젝트명}`: 소문자, 하이픈 없이 단일 단어 (예: `ordersystem`, `commerce`)
- `{도메인}`: 서비스 핵심 도메인명 소문자 (예: `user`, `order`, `product`)
- 예시: `com.ordersystem.user`, `com.ordersystem.order`

## 2. 클래스 네이밍

### 2.1. domain 계층
순수 Java. 프레임워크/JPA 어노테이션 완전 금지.

| 역할 | 규칙 | 예시 |
|---|---|---|
| 도메인 엔티티 | `{Domain}` | `User`, `Order` |
| Value Object (record) | 의미 있는 이름 | `UserId`, `Money`, `Email` |

### 2.2. port/in/ 계층 — 입력 포트
UseCase를 **인터페이스**로 정의한다. 구현체는 adapter가 아닌 별도 service 레이어에 둔다.

| 역할 | 규칙 | 예시 |
|---|---|---|
| UseCase 인터페이스 | `{Action}{Domain}UseCase` | `CreateUserUseCase`, `FindUserUseCase` |

### 2.3. port/out/ 계층 — 출력 포트
인프라 기술에 의존하지 않는 순수 인터페이스.

| 역할 | 규칙 | 예시 |
|---|---|---|
| Repository 인터페이스 | `{Domain}Repository` | `UserRepository`, `OrderRepository` |
| 외부 서비스 포트 | `{Domain}Port` | `UserPort`, `NotificationPort` |

### 2.4. adapter/in/web/ 계층 — Web 어댑터

| 역할 | 규칙 | 예시 |
|---|---|---|
| Controller | `{Domain}Controller` | `UserController` |
| Request DTO (record) | `{Action}{Domain}Request` | `CreateUserRequest`, `UpdateOrderRequest` |
| Response DTO (record) | `{Domain}Response` | `UserResponse`, `OrderSummaryResponse` |

### 2.5. adapter/out/persistence/ 계층 — 영속성 어댑터

| 역할 | 규칙 | 예시 |
|---|---|---|
| JPA Entity | `{Domain}JpaEntity` | `UserJpaEntity` |
| JPA Repository (Spring Data) | `{Domain}JpaRepository` | `UserJpaRepository` |
| Repository 구현체 | `{Domain}RepositoryAdapter` | `UserRepositoryAdapter` |

### 2.6. adapter/out/external/ 계층 — 외부 서비스 어댑터

| 역할 | 규칙 | 예시 |
|---|---|---|
| Feign Client | `{TargetDomain}Client` | `UserClient`, `OrderClient` |
| Feign Client 구현 어댑터 | `{TargetDomain}ClientAdapter` | `UserClientAdapter` |

### 2.7. UseCase 구현체 위치
UseCase 구현체는 `application/` 패키지에 둔다. port/in 인터페이스를 implements한다.

| 역할 | 규칙 | 예시 |
|---|---|---|
| UseCase 구현 서비스 | `{Domain}Service` | `UserService`, `OrderService` |

```
com.ordersystem.user
  ├── application/
  │   └── UserService.java      (implements CreateUserUseCase, FindUserUseCase)
  ├── port/in/
  │   ├── CreateUserUseCase.java (interface)
  │   └── FindUserUseCase.java   (interface)
  └── ...
```

### 2.8. global 계층

| 역할 | 규칙 | 예시 |
|---|---|---|
| 공통 응답 (record) | `ApiResponse` | `ApiResponse<T>` |
| 비즈니스 예외 | `BusinessException` | — |
| 시스템 에러 코드 | `SystemErrorCode` | — |
| 비즈니스 결과 코드 | `BusinessResultCode` | — |
| 전역 예외 핸들러 | `GlobalExceptionHandler` | — |

## 3. 메서드 네이밍

| 용도 | 규칙 | 예시 |
|---|---|---|
| 조회 | `find{Domain}By{Condition}` | `findUserById` |
| 생성 | `create{Domain}` | `createUser` |
| 수정 | `update{Domain}` | `updateOrderStatus` |
| 삭제 | `delete{Domain}` | `deleteUser` |
| 도메인 상태 변경 | 의미 있는 동사 | `confirm()`, `cancel()`, `ship()` |

## 4. 기타 규칙

- **상수:** `UPPER_SNAKE_CASE`
- **변수/필드:** `camelCase`
- **DB 테이블명:** `UPPER_SNAKE_CASE` (예: `USER_ORDER`)
- **브랜치명:** `feature/{domain}-{기능명}` (예: `feature/user-login`)

## 5. 의존성 방향 규칙 (엄수)

```
adapter → port(interface) → domain
```

- `adapter`는 `port` 인터페이스에만 의존한다 (구현체에 직접 의존 금지)
- `domain`은 어떤 외부 계층도 import하지 않는다
- `port`는 `domain` 타입만 사용한다 (JPA Entity, DTO 사용 금지)
