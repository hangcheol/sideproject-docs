# 🏷️ Naming Conventions

## 1. 패키지 구조

```
com.{프로젝트명}.{도메인}
  ├── global/          (서비스 전역 공통 — 응답 래퍼, 예외, 설정)
  ├── interfaces/      (Controller, Request/Response DTO)
  ├── application/     (UseCase — 트랜잭션 경계)
  ├── domain/          (순수 Java Entity, VO, Repository 인터페이스)
  └── infrastructure/  (JPA Entity, JPA Repository 구현체, Feign Client)
```

- `{프로젝트명}`: 소문자, 하이픈 없이 단일 단어 권장 (예: `ordersystem`, `commerce`)
- `{도메인}`: 해당 서비스의 핵심 도메인명 소문자 (예: `user`, `order`, `product`)
- 예시: `com.ordersystem.user`, `com.ordersystem.order`

## 2. 클래스 네이밍

### 2.1. interfaces 계층
| 역할 | 규칙 | 예시 |
|---|---|---|
| Controller | `{Domain}Controller` | `UserController` |
| Request DTO (record) | `{Action}{Domain}Request` | `CreateUserRequest`, `UpdateOrderRequest` |
| Response DTO (record) | `{Domain}Response` | `UserResponse`, `OrderSummaryResponse` |

### 2.2. application 계층
| 역할 | 규칙 | 예시 |
|---|---|---|
| UseCase | `{Domain}UseCase` | `UserUseCase`, `OrderUseCase` |

### 2.3. domain 계층
| 역할 | 규칙 | 예시 |
|---|---|---|
| 순수 Entity | `{Domain}` | `User`, `Order` |
| Value Object (record) | `{Domain}Id` 또는 의미 있는 이름 | `UserId`, `Money` |
| Repository 인터페이스 | `{Domain}Repository` | `UserRepository` |

### 2.4. infrastructure 계층
| 역할 | 규칙 | 예시 |
|---|---|---|
| JPA Entity | `{Domain}JpaEntity` | `UserJpaEntity` |
| JPA Repository | `{Domain}JpaRepository` | `UserJpaRepository` |
| Feign Client | `{TargetService}Client` | `UserServiceClient`, `OrderServiceClient` |

### 2.5. global 계층
| 역할 | 규칙 | 예시 |
|---|---|---|
| 공통 응답 (record) | `ApiResponse` | `ApiResponse<T>` |
| 비즈니스 예외 | `BusinessException` | — |
| 에러 코드 Enum | `ErrorCode` | — |
| 전역 예외 핸들러 | `GlobalExceptionHandler` | — |

## 3. 메서드 네이밍
- 조회: `find{Domain}By{Condition}` (예: `findUserById`)
- 생성: `create{Domain}` (예: `createUser`)
- 수정: `update{Domain}` (예: `updateOrderStatus`)
- 삭제: `delete{Domain}` (예: `deleteUser`)
- 도메인 상태 변경 메서드: 의미 있는 동사 (예: `confirm()`, `cancel()`, `ship()`)

## 4. 기타 규칙
- **상수:** `UPPER_SNAKE_CASE`
- **변수/필드:** `camelCase`
- **DB 테이블명:** `UPPER_SNAKE_CASE` (예: `USER_ORDER`)
- **브랜치명:** `feature/{domain}-{기능명}` (예: `feature/user-login`)
