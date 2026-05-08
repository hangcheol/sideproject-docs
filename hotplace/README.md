# Hotplace Simulator — 프로젝트 스펙

포켓몬 고 스타일의 2D 소셜 핫플레이스 플랫폼.
사용자가 실제 위치를 인증하고 채팅·피드를 공유하는 위치 기반 소셜 서비스.

## 문서 목록

| 파일 | 내용 |
|------|------|
| [service-spec.md](service-spec.md) | 서비스별 도메인·API·포트 상세 스펙 |
| [tech-stack.md](tech-stack.md) | Hotplace 전용 기술 스택 (PostGIS, WebSocket, Redis 등) |
| [feign-contracts.md](feign-contracts.md) | 서비스 간 Feign 의존관계 및 fallback 규약 |

## 서비스 목록

| 서비스 | 포트 | 역할 |
|--------|------|------|
| user-service | 8081 | 회원가입, OAuth2(Kakao/Google), 프로필, 친구 관계 |
| location-service | 8082 | 위치 저장, GPS 현장인증(PostGIS), 혼잡도 |
| chat-service | 8083 | WebSocket(STOMP) 실시간 채팅, 채널(Room) 관리 |
| feed-service | 8084 | 사진 업로드, 게시글(지역·시간대 태깅) |
| hotplace-frontend | 3000 | React 18 + TypeScript + Zustand |

## 제외 항목

- 비로그인(Guest) 세션 기능 일체
- NPC 생성/이동 로직 (프론트 전용 기능)
- 외부 API 실제 연동 (SeoulCityApi, SkApi) — 인터페이스·Adapter 골격만
