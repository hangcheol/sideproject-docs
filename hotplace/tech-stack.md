# Hotplace — 기술 스택

## Backend 공통

| 항목 | 버전/선택 |
|------|----------|
| Java | 21 (Virtual Threads 필수) |
| Spring Boot | 3.4+ |
| Spring Data JPA | Spring Boot 종속 |
| Spring Security | OAuth2 Resource Server (JWT) |
| OpenFeign | Spring Cloud 2024 |
| Resilience4j | 2.x (모든 Feign에 fallback 필수) |
| Gradle | 8.x |

## 서비스별 특이 의존성

### user-service
```groovy
implementation 'org.springframework.boot:spring-boot-starter-oauth2-resource-server'
implementation 'org.springframework.boot:spring-boot-starter-security'
```

### location-service
```groovy
// PostGIS
implementation 'org.hibernate:hibernate-spatial'
runtimeOnly 'org.postgresql:postgresql'

// Redis (혼잡도 캐시)
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

### chat-service
```groovy
// WebSocket + STOMP
implementation 'org.springframework.boot:spring-boot-starter-websocket'

// Redis Pub/Sub
implementation 'org.springframework.boot:spring-boot-starter-data-redis'

// DB (채팅 기록)
runtimeOnly 'org.postgresql:postgresql'
```

### feed-service
```groovy
runtimeOnly 'org.postgresql:postgresql'
```

## DB 전략

| 서비스 | DB | 특이사항 |
|--------|-----|---------|
| user-service | PostgreSQL | 일반 RDB |
| location-service | PostgreSQL + PostGIS | `geometry(Point, 4326)` 컬럼 |
| chat-service | PostgreSQL | 채팅 기록 영속화 |
| feed-service | PostgreSQL | 피드 메타데이터 |
| chat-service, location-service | Redis | 메시지 브로드캐스트 + 혼잡도 캐시 공유 |

## 로컬 개발 환경

Docker Compose로 PostgreSQL + Redis를 띄워야 한다.
→ `C:\Project\SideProject\firstBusiness\hotplace\docker-compose.yml` 참조

## Frontend

| 항목 | 버전 |
|------|------|
| React | 18 |
| TypeScript | 5.x |
| Vite | 5.x |
| Zustand | 4.x |
| React Router | v6 |
| Axios | 1.x |
| Leaflet.js | 1.9.x (지도) |
