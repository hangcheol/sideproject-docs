# Hotplace — Feign 의존관계 및 fallback 규약

## 서비스 간 의존관계

```
user-service (8081)        ← 다른 서비스가 조회
location-service (8082)    → user-service (토큰 검증, 유저 확인)
chat-service (8083)        → user-service (발신자 프로필)
                           → location-service (Place 유효성)
feed-service (8084)        → user-service (작성자 프로필)
                           → location-service (Place 유효성)
```

## Feign Client 구현 규약

### 위치
```
adapter/out/external/
  ├── {TargetDomain}Client.java        (Feign 인터페이스)
  └── {TargetDomain}ClientAdapter.java (port/out Port 인터페이스 구현체)
```

### port/out 인터페이스 정의 예시
```java
// port/out/UserPort.java
public interface UserPort {
    Optional<UserInfo> findUserById(UUID userId);
    boolean existsUser(UUID userId);
}
```

### Feign Client 예시
```java
// adapter/out/external/UserClient.java
@FeignClient(name = "user-service", url = "${services.user-service.url}",
             fallbackFactory = UserClientFallbackFactory.class)
public interface UserClient {
    @GetMapping("/api/v1/users/{id}")
    ApiResponse<UserInfo> getUser(@PathVariable UUID id);
}
```

### fallback 필수 구현
```java
// adapter/out/external/UserClientFallbackFactory.java
@Component
public class UserClientFallbackFactory implements FallbackFactory<UserClient> {
    @Override
    public UserClient create(Throwable cause) {
        return id -> ApiResponse.fail("USER_SERVICE_UNAVAILABLE", "유저 서비스 일시 불가");
    }
}
```

### ClientAdapter 예시
```java
// adapter/out/external/UserClientAdapter.java
@Component
@RequiredArgsConstructor
public class UserClientAdapter implements UserPort {

    private final UserClient userClient;

    @Override
    public Optional<UserInfo> findUserById(UUID userId) {
        ApiResponse<UserInfo> response = userClient.getUser(userId);
        return response.success() ? Optional.ofNullable(response.data()) : Optional.empty();
    }
}
```

## 각 서비스별 외부 포트 목록

### location-service
| Port 인터페이스 | 구현 Client | fallback 동작 |
|----------------|------------|--------------|
| `UserPort` | `UserClient` | userId 검증 실패 시 `BusinessException(USER_NOT_FOUND)` |

### chat-service
| Port 인터페이스 | 구현 Client | fallback 동작 |
|----------------|------------|--------------|
| `UserPort` | `UserClient` | 발신자 정보 없을 시 기본값("알 수 없음") 반환 |
| `PlacePort` | `LocationClient` | Place 확인 실패 시 `BusinessException(PLACE_NOT_FOUND)` |

### feed-service
| Port 인터페이스 | 구현 Client | fallback 동작 |
|----------------|------------|--------------|
| `UserPort` | `UserClient` | 작성자 정보 없을 시 기본값 반환 |
| `PlacePort` | `LocationClient` | Place 확인 실패 시 `BusinessException(PLACE_NOT_FOUND)` |

## application.yml Feign 설정

```yaml
services:
  user-service:
    url: http://localhost:8081
  location-service:
    url: http://localhost:8082

feign:
  circuitbreaker:
    enabled: true

resilience4j:
  circuitbreaker:
    instances:
      user-service:
        slidingWindowSize: 10
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
      location-service:
        slidingWindowSize: 10
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
```
