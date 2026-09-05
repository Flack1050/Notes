---
tags:
  - docker
  - compose
  - devops
---

# 🧩 Docker Compose

Связано: [[🐳Docker]] · [[🔄CI-CD Pipelines]]

Декларативное описание multi-container приложения в одном YAML-файле.

## Пример: web + Postgres + Redis

```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/appdb
      - REDIS_URL=redis://cache:6379
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=appdb
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 5s
      timeout: 5s
      retries: 5

  cache:
    image: redis:7-alpine

volumes:
  pgdata:
```

## Команды

```bash
docker compose up -d              # поднять всё в фоне
docker compose up -d --build      # с пересборкой
docker compose ps
docker compose logs -f web
docker compose exec web sh
docker compose down                # остановить + удалить контейнеры/сети
docker compose down -v             # + удалить volumes (данные!)
docker compose restart web
docker compose config              # проверить итоговый рендер YAML
```

## Профили и окружения

```yaml
services:
  debug-tools:
    image: busybox
    profiles: ["debug"]
```

```bash
docker compose --profile debug up -d
```

Раздельные env-файлы:

```bash
docker compose --env-file .env.prod up -d
```

## override-файл для локальной разработки

`docker-compose.override.yml` подхватывается автоматически поверх основного `docker-compose.yml` — удобно держать в нём volume-mount исходников и debug-порты, не трогая основной файл.

```yaml
services:
  web:
    volumes:
      - ./src:/app/src
    command: npm run dev
```

> [!tip]
> Секреты (пароли, ключи) не хардкодь в compose-файле — выноси в `.env` и добавляй `.env` в `.gitignore`.

#docker #compose #devops
