# 🌐 API Conventions

## 1. URL 설계 원칙
- 기본 prefix: `/api/v1/{resource}`
- `{resource}`는 **복수형 명사** 사용 (예: `users`, `orders`)
- 단어 구분: 하이픈(`-`) 사용 (예: `/api/v1/order-items`)
- 동사 사용 금지 — HTTP Method로 행위를 표현

### 표준 URL 패턴
| HTTP Method | URL | 의미 |
|---|---|---|
| `GET` | `/api/v1/users` | 목록 조회 |
| `GET` | `/api/v1/users/{id}` | 단건 조회 |
| `POST` | `/api/v1/users` | 생성 |
| `PUT` | `/api/v1/users/{id}` | 전체 수정 |
| `PATCH` | `/api/v1/users/{id}` | 부분 수정 |
| `DELETE` | `/api/v1/users/{id}` | 삭제 |

## 2. 공통 응답 포맷 (ApiResponse<T>)

모든 Controller는 `ApiResponse<T>`로 응답을 래핑한다.

### 성공 응답
```json
{
  "success": true,
  "code": "S200",
  "message": "OK",
  "data": { ... }
}
```

### 실패 응답
```json
{
  "success": false,
  "code": "U0001",
  "message": "사용자를 찾을 수 없습니다.",
  "data": null
}
```

### Controller 작성 예시
```java
@GetMapping("/{id}")
public ResponseEntity<ApiResponse<UserResponse>> getUser(@PathVariable Long id) {
    return ResponseEntity.ok(ApiResponse.ok(userUseCase.getUser(id)));
}

@PostMapping
public ResponseEntity<ApiResponse<UserResponse>> createUser(
    @Valid @RequestBody CreateUserRequest request
) {
    return ResponseEntity.status(HttpStatus.CREATED)
        .body(ApiResponse.ok(userUseCase.createUser(request)));
}
```

## 3. 에러 코드 체계

### 에러 코드 형식
- 패턴: `{서비스 이니셜}{4자리 숫자}`
- 서비스 이니셜 예시: `U` (user), `O` (order), `P` (product)

| 코드 범위 | 분류 |
|---|---|
| `{X}0001 ~ {X}0099` | 조회 실패 |
| `{X}0100 ~ {X}0199` | 생성 실패 |
| `{X}0200 ~ {X}0299` | 수정 실패 |
| `{X}0300 ~ {X}0399` | 삭제 실패 |
| `{X}0400 ~ {X}0499` | 비즈니스 규칙 위반 |

### ErrorCode Enum 작성 예시
```java
@Getter
public enum ErrorCode {
    // 조회
    USER_NOT_FOUND(404, "U0001", "사용자를 찾을 수 없습니다."),
    // 비즈니스 규칙
    USER_ALREADY_EXISTS(409, "U0400", "이미 존재하는 사용자입니다."),
    ;

    private final int httpStatus;
    private final String code;
    private final String message;

    ErrorCode(int httpStatus, String code, String message) {
        this.httpStatus = httpStatus;
        this.code = code;
        this.message = message;
    }
}
```

**규칙:** HTTP Status와 비즈니스 에러 코드는 반드시 분리한다.
- HTTP Status: 요청 처리 성공/실패 (네트워크 레벨)
- 비즈니스 에러 코드: 도메인 규칙 위반 여부

## 4. OpenFeign 서비스 간 통신

### 응답 포맷
다른 서비스를 Feign으로 호출할 때도 `ApiResponse<T>`로 응답을 수신한다.

```java
@FeignClient(name = "user-service", url = "${feign.user-service.url}")
public interface UserServiceClient {
    @GetMapping("/api/v1/users/{id}")
    ApiResponse<UserResponse> getUser(@PathVariable Long id);
}
```

### Resilience4j Fallback 필수 적용
```java
@FeignClient(
    name = "user-service",
    url = "${feign.user-service.url}",
    fallback = UserServiceClientFallback.class
)
```

## 5. 유효성 검증
- Request DTO (record)에 `@Valid` 어노테이션 필수 (`@NotNull`, `@NotBlank`, `@Size` 등)
- Controller 파라미터에 `@Valid @RequestBody` 명시
