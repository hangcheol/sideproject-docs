# Hotplace — 서비스별 상세 스펙

## 1. user-service (포트: 8081)

### 패키지 루트
`com.hotplace.user`

### 도메인 엔티티
```
User
  - id: UUID
  - email: String
  - nickname: String
  - profileImageUrl: String
  - oauthProvider: enum { KAKAO, GOOGLE }
  - createdAt: LocalDateTime

Friendship
  - id: UUID
  - requesterId: UUID
  - receiverId: UUID
  - status: enum { PENDING, ACCEPTED, BLOCKED }
```

### UseCase 인터페이스 (port/in)
- `RegisterUserUseCase` — OAuth2 회원가입/로그인
- `FindUserUseCase` — 프로필 조회
- `ManageFriendshipUseCase` — 친구 요청, 수락, 차단

### Repository 인터페이스 (port/out)
- `UserRepository`
- `FriendshipRepository`

### 핵심 API
```
POST /api/v1/users/oauth          # OAuth2 로그인 (Kakao/Google)
GET  /api/v1/users/{id}           # 프로필 조회
PUT  /api/v1/users/{id}/profile   # 프로필 수정
POST /api/v1/friends/request      # 친구 요청
PUT  /api/v1/friends/{id}/accept  # 친구 수락
GET  /api/v1/friends              # 친구 목록
```

### 외부 의존 없음 (다른 서비스로부터 조회당하는 입장)

---

## 2. location-service (포트: 8082)

### 패키지 루트
`com.hotplace.location`

### 도메인 엔티티
```
Place
  - id: UUID
  - name: String
  - address: String
  - latitude: Double
  - longitude: Double
  - category: String
  - geometry: Point (SRID: 4326, PostGIS)

UserLocation
  - id: UUID
  - userId: UUID
  - placeId: UUID
  - latitude: Double
  - longitude: Double
  - isVerified: Boolean
  - visitedAt: LocalDateTime

Congestion
  - id: UUID
  - placeId: UUID
  - level: enum { LOW, MEDIUM, HIGH }
  - measuredAt: LocalDateTime
  - source: enum { API, CACHE }
```

### UseCase 인터페이스 (port/in)
- `SaveUserLocationUseCase` — 위치 저장
- `VerifyLocationUseCase` — GPS 현장인증 (ST_DWithin 반경 50m)
- `FindCongestionUseCase` — 혼잡도 조회 (Redis 캐시 5분 TTL)
- `FindPlaceUseCase` — 장소 조회

### Repository 인터페이스 (port/out)
- `PlaceRepository`
- `UserLocationRepository`
- `CongestionRepository`

### 외부 포트 (port/out)
- `UserPort` — user-service 토큰 검증, 유저 존재 확인 (Feign)
- `CongestionExternalPort` — 외부 혼잡도 API (인터페이스만, 실 연동 제외)

### 핵심 API
```
GET  /api/v1/places                  # 장소 목록
GET  /api/v1/places/{id}             # 장소 상세
GET  /api/v1/places/congestion       # 전체 지도 혼잡도 조회
POST /api/v1/locations               # 위치 저장
POST /api/v1/locations/verify        # GPS 현장인증 (ST_DWithin 반경 50m)
```

### 특이사항
- DB: PostgreSQL + PostGIS
- 현장인증: `ST_DWithin(geometry, ST_SetSRID(ST_MakePoint(:lon, :lat), 4326), 50)` — 반경 50m
- 혼잡도: Redis 캐시 5분 TTL (`congestion:place:{placeId}` 키)
- `PlaceJpaEntity`에 `@Column(columnDefinition = "geometry(Point, 4326)")` 사용

---

## 3. chat-service (포트: 8083)

### 패키지 루트
`com.hotplace.chat`

### 도메인 엔티티
```
ChatRoom
  - id: UUID
  - placeId: UUID
  - roomType: enum { PLACE, GROUP }
  - createdAt: LocalDateTime

ChatMessage
  - id: UUID
  - roomId: UUID
  - senderId: UUID
  - content: String
  - messageType: enum { TEXT, IMAGE }
  - sentAt: LocalDateTime
```

### UseCase 인터페이스 (port/in)
- `EnterChatRoomUseCase` — 채팅방 입장 (Place 유효성 확인 후)
- `SendMessageUseCase` — WebSocket 메시지 전송
- `FindChatHistoryUseCase` — 채팅 기록 조회

### Repository 인터페이스 (port/out)
- `ChatRoomRepository`
- `ChatMessageRepository`

### 외부 포트 (port/out)
- `UserPort` — 발신자 프로필 조회 (Feign)
- `PlacePort` — 채팅방 생성 시 Place 유효성 확인 (Feign)

### 핵심 API
```
POST /api/v1/chat/rooms/{placeId}    # 채팅방 입장 (없으면 생성)
GET  /api/v1/chat/rooms              # 채팅방 목록
GET  /api/v1/chat/rooms/{id}/messages # 채팅 기록 조회
POST /api/v1/chat/messages           # WebSocket 메시지 전송 (STOMP)

WebSocket STOMP:
  SUBSCRIBE: /topic/room/{roomId}    # 채팅방 구독
  SEND:      /app/chat.send          # 메시지 전송
```

### 특이사항
- WebSocket(STOMP) + Redis Pub/Sub 브로드캐스트
- `WebSocketConfig`: SockJS fallback, STOMP endpoint `/ws`
- Redis 채널: `chat:room:{roomId}`

---

## 4. feed-service (포트: 8084)

### 패키지 루트
`com.hotplace.feed`

### 도메인 엔티티
```
Feed
  - id: UUID
  - authorId: UUID
  - placeId: UUID
  - imageUrl: String
  - content: String
  - takenAt: LocalDateTime      (사진 촬영 시각)
  - latitude: Double
  - longitude: Double
  - createdAt: LocalDateTime
```

### UseCase 인터페이스 (port/in)
- `CreateFeedUseCase` — 사진+게시글 업로드 (지역·시간대 태깅)
- `FindFeedUseCase` — 피드 목록 조회 (장소별)

### Repository 인터페이스 (port/out)
- `FeedRepository`

### 외부 포트 (port/out)
- `UserPort` — 작성자 프로필 조회 (Feign)
- `PlacePort` — 게시글 태깅 시 Place 유효성 확인 (Feign)

### 핵심 API
```
POST /api/v1/feeds                   # 사진+게시글 업로드 (Multipart)
GET  /api/v1/feeds?placeId={id}      # 특정 장소 피드 조회
GET  /api/v1/feeds/{id}              # 피드 상세
DELETE /api/v1/feeds/{id}            # 피드 삭제
```

### 특이사항
- MultipartFile 수신 후 로컬 저장 (`/uploads/feeds/`) — 추후 S3 교체 가능 구조
- `StoragePort` 인터페이스로 추상화 (LocalStorageAdapter 구현)

---

## 5. hotplace-frontend (포트: 3000)

### 기술 스택
- React 18 + TypeScript + Vite
- Zustand (전역 상태)
- React Router v6
- Axios (공통 인터셉터)
- Leaflet.js (지도 렌더링)

### 폴더 구조
```
src/
  api/      (userApi.ts, locationApi.ts, chatApi.ts, feedApi.ts)
  store/    (userStore.ts, locationStore.ts, chatStore.ts, feedStore.ts)
  pages/    (MapPage.tsx, LoginPage.tsx, FeedPage.tsx, ChatPage.tsx, ProfilePage.tsx)
  components/
  hooks/
  types/
```
