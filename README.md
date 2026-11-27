# GateShift

**비행편이 바뀌면, 내 일정은 어떻게 달라질까?**

GateShift는 항공편의 출발 시각이나 탑승구가 바뀌면 공항에서 할 일과 도착 후 호텔 체크인 계획을 다시 확인해 주는 웹 서비스다. 인천공항 제1여객터미널 출국심사 이후 일부 구역을 첫 데모로 다룬다.

Docker 기반 개발환경을 구성했다. 2인·2주 기준 MVP와 검증 계획은 아래 문서에 정리했다.

| 문서 | 내용 |
| --- | --- |
| [서비스 기획](docs/product.md) | 사용자, 핵심 흐름, MVP, 발표 시나리오 |
| [아키텍처](docs/architecture.md) | 시스템 구성, 데이터 출처, 영향 재평가 방식 |
| [2주 구현 계획](docs/implementation-plan.md) | 역할, 진행 순서, 검증과 배포 기준 |
| [협업 컨벤션](docs/conventions.md) | 이슈, 브랜치, 커밋, PR, 병합 |
| [Codex 작업 가이드](codex.md) | 에이전트 작업 범위와 검증 기준 |

## 로컬 실행

Docker와 Docker Compose v2가 필요하다. 저장소 루트에서 다음 명령을 실행한다.

```bash
docker compose up --build -d --wait
docker compose logs -f backend
```

- 화면은 [localhost:8080](http://localhost:8080), API와 DB 상태는 [localhost:8080/api/health](http://localhost:8080/api/health)에서 확인한다. 초기 빌드와 MySQL 부팅이 끝나면 응답이 `{"status":"UP"}`으로 표시된다.
- 8080 포트를 쓰고 있다면 [환경변수 예시](.env.example)를 `.env`로 복사하고 `GATESHIFT_PORT`를 바꾼다. 기본 DB 비밀번호는 로컬 전용이다. MySQL과 Spring Boot는 Compose 내부 네트워크로 연결한다.
- `docker compose down`으로 종료한다. MySQL 데이터는 `mysql-data` 볼륨에 보존된다.

## 모듈별 확인

| 모듈 | 환경 | 검증 명령 |
| --- | --- | --- |
| `backend/` | Java 21, Spring Boot 3.5, Maven 3.9, JPA, Flyway | `mvn -f backend/pom.xml verify` |
| `frontend/` | Vue 3, TypeScript, Vite, Node.js 24 | `npm --prefix frontend ci`, `npm --prefix frontend run build` |
| DB | MySQL 8.4 | Compose 시작 후 `/api/health` 확인 |

DB 스키마 변경은 `backend/src/main/resources/db/migration/` 아래 Flyway SQL로 관리한다. 운항 API와 AI 연동은 해당 기능을 개발할 때 연결한다.
