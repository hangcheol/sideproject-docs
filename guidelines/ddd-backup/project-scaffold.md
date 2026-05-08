# 🏗️ Project Scaffold Convention

## 1. 레포지토리 명명 규칙
- 패턴: `{domain}-service`
- 예시: `user-service`, `order-service`, `product-service`
- 각 MSA 서비스는 독립된 Git 레포지토리로 관리한다 (`architecture.md` Domain Isolation 원칙)

## 2. 포트 배정 규칙
| 용도 | 범위 |
|---|---|
| 마이크로서비스 | 8081부터 순서대로 +1 |
| API Gateway (미래) | 8080 |

## 3. 서비스 표준 폴더 구조

```
{domain}-service/
├── src/
│   ├── main/
│   │   ├── java/com/{프로젝트명}/{domain}/
│   │   │   ├── global/
│   │   │   │   ├── common/
│   │   │   │   │   └── ApiResponse.java          (record — 공통 응답 래퍼)
│   │   │   │   └── error/
│   │   │   │       ├── ErrorCode.java             (enum)
│   │   │   │       ├── BusinessException.java
│   │   │   │       └── GlobalExceptionHandler.java
│   │   │   ├── interfaces/
│   │   │   │   ├── {Domain}Controller.java
│   │   │   │   └── dto/
│   │   │   │       ├── {Action}{Domain}Request.java  (record)
│   │   │   │       └── {Domain}Response.java         (record)
│   │   │   ├── application/
│   │   │   │   └── {Domain}UseCase.java
│   │   │   ├── domain/
│   │   │   │   ├── {Domain}.java                  (순수 Java — @Entity 금지)
│   │   │   │   ├── {Domain}Id.java                (VO, record)
│   │   │   │   └── {Domain}Repository.java        (interface)
│   │   │   └── infrastructure/
│   │   │       ├── {Domain}JpaEntity.java         (@Entity는 여기에만)
│   │   │       ├── {Domain}JpaRepository.java
│   │   │       └── {TargetService}Client.java     (OpenFeign — 의존 있을 때만)
│   │   └── resources/
│   │       └── application.yml
│   └── test/
│       └── java/com/{프로젝트명}/{domain}/
│           └── application/
│               └── {Domain}UseCaseTest.java
├── build.gradle
├── settings.gradle
└── Dockerfile
```

## 4. build.gradle 표준 의존성
```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.4.0'
    id 'io.spring.dependency-management' version '1.1.4'
}

java {
    toolchain { languageVersion = JavaLanguageVersion.of(21) }
}

dependencies {
    // Core
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-validation'

    // QueryDSL 6.x
    implementation 'com.querydsl:querydsl-jpa:6.x:jakarta'
    annotationProcessor 'com.querydsl:querydsl-apt:6.x:jakarta'

    // OpenFeign + Resilience4j
    implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'
    implementation 'io.github.resilience4j:resilience4j-spring-boot3'

    // DB
    runtimeOnly 'org.mariadb.jdbc:mariadb-java-client'

    // Test
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

## 5. application.yml 표준 설정
```yaml
server:
  port: ${SERVICE_PORT:808x}

spring:
  threads:
    virtual:
      enabled: true          # Java 21 Virtual Threads
  datasource:
    url: jdbc:mariadb://localhost:3306/{domain}_db
    driver-class-name: org.mariadb.jdbc.Driver
  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false
```

## 6. Global 패키지 필수 구현체

### ApiResponse (record)
```java
package com.{프로젝트명}.{domain}.global.common;

public record ApiResponse<T>(
    boolean success,
    String code,
    String message,
    T data
) {
    public static <T> ApiResponse<T> ok(T data) {
        return new ApiResponse<>(true, "S200", "OK", data);
    }

    public static ApiResponse<Void> ok() {
        return new ApiResponse<>(true, "S200", "OK", null);
    }

    public static <T> ApiResponse<T> fail(String code, String message) {
        return new ApiResponse<>(false, code, message, null);
    }
}
```

### BusinessException
```java
package com.{프로젝트명}.{domain}.global.error;

public class BusinessException extends RuntimeException {
    private final ErrorCode errorCode;

    public BusinessException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }

    public ErrorCode getErrorCode() { return errorCode; }
}
```
