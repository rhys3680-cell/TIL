# Docker로 PostgreSQL + pgvector 로컬 DB 띄우기

## 배경

화장품 성분 DB 프로젝트에서 DB 선택 시 고려한 내용과 설정 방법.

## DB 선택 기준

| | SQLite | PostgreSQL | Supabase |
|---|---|---|---|
| 설정 난이도 | 매우 쉬움 | 보통 | 쉬움 |
| 로컬 개발 | 파일 하나로 끝 | Docker 필요 | 클라우드 |
| 벡터 검색 | 불가 | pgvector 확장 | pgvector 내장 |
| 배포 | 어려움 | 직접 서버 필요 | 바로 가능 |
| 비용 | 무료 | 무료 (셀프호스팅) | 무료 플랜 있음 |

**선택: PostgreSQL + pgvector**

- 관계형 데이터 (성분/제품/매핑 테이블) 처리에 적합
- pgvector로 성분 임베딩 기반 유사 제품 추천까지 커버
- 나중에 Supabase로 마이그레이션 용이

## 설정

### docker-compose.yml

```yaml
services:
  db:
    image: pgvector/pgvector:pg17
    container_name: cosmetic-db
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: cosmetic
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### 실행

```bash
docker compose up -d
```

### pgvector 활성화

DB 접속 후 아래 실행:

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

## 볼륨 경로 변경 (외장 SSD 등)

용량이 필요하면 볼륨을 named volume 대신 bind mount로 변경:

```yaml
volumes:
  - /path/to/external/ssd/pgdata:/var/lib/postgresql/data
```

## 참고

- [pgvector/pgvector Docker Hub](https://hub.docker.com/r/pgvector/pgvector)
