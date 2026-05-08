# 🏗️ Infrastructure & Deployment Guidelines

## 1. Cloud Environment
- **Provider:** AWS EC2 (Free Tier - t2.micro/t3.micro)
- **Resource Constraint:** RAM 1GB (메모리 사용량 최적화 필수)
- **Strategy:** 가용 리소스가 적으므로 불필요한 컨테이너 실행을 지양하고, JVM 힙 메모리를 제한한다.

## 2. Standard Dockerfile (Java 21)
모든 마이크로서비스는 다음의 경량화된 Dockerfile 형식을 반드시 준수해야 한다.

```dockerfile
# Build 스테이지
FROM gradle:8-jdk21 AS build
COPY --chown=gradle:gradle . /home/gradle/src
WORKDIR /home/gradle/src
RUN ./gradlew build --no-daemon

# Run 스테이지 (JRE 전용 경량 이미지 사용)
FROM eclipse-temurin:21-jre-alpine
COPY --from=build /home/gradle/src/build/libs/*.jar app.jar

# 타임존 설정 (Asia/Seoul)
RUN apk add --no-cache tzdata && \
    cp /usr/share/zoneinfo/Asia/Seoul /etc/localtime && \
    echo "Asia/Seoul" > /etc/timezone

# 프리티어 메모리 제약 반영 (Xmx512m)
ENTRYPOINT ["java", "-Xmx512m", "-Dspring.profiles.active=prod", "-jar", "/app.jar"]