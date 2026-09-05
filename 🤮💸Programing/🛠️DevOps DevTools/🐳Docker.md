---
tags: [docker, containers, devops]
---

# 🐳 Docker

Связано: [[🧩Docker Compose]] · [[☸️Kubernetes]] · [[🔄CI-CD Pipelines]]

## Ключевые понятия

- **Image** — неизменяемый шаблон (слои файловой системы + метаданные).
- **Container** — запущенный экземпляр image.
- **Dockerfile** — рецепт сборки image.
- **Registry** — хранилище images (Docker Hub, GHCR, свой Harbor/Nexus).
- **Volume** — постоянное хранилище данных вне жизненного цикла контейнера.
- **Network** — виртуальная сеть для связи контейнеров между собой.

## Базовые команды

```bash
# инфо
docker version
docker info

# images
docker images
docker pull nginx:1.27
docker build -t myapp:1.0 .
docker rmi myapp:1.0

# containers
docker run -d --name web -p 8080:80 nginx:1.27
docker ps            # запущенные
docker ps -a         # все, включая остановленные
docker stop web
docker rm web
docker logs -f web
docker exec -it web sh

# volumes / networks
docker volume ls
docker volume create mydata
docker network ls
docker network create mynet

# очистка
docker system prune -a --volumes   # осторожно, удаляет всё неиспользуемое
```

> [!note] fish shell
> Если работаешь в fish, экспорт переменных для докера делается через `set -x`, а не `export`:
> ```fish
> set -x DOCKER_BUILDKIT 1
> ```

## Dockerfile — пример (Node.js multi-stage)

```dockerfile
# --- build stage ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# --- runtime stage ---
FROM node:20-alpine
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
USER node
CMD ["node", "dist/main.js"]
```

### Best practices
- Multi-stage build → меньше финальный image.
- Не запускай процесс от root (`USER node`).
- `.dockerignore` — исключай `node_modules`, `.git`, секреты.
- Пиннуй версии базовых images (`node:20-alpine`, а не `node:latest`).
- Один процесс на контейнер.
- Кэшируй слои: сначала копируй `package.json`, потом остальной код.
- Используй `HEALTHCHECK` для критичных сервисов.

## .dockerignore пример

```
.git
node_modules
*.log
.env
Dockerfile
```

## Отладка

```bash
docker inspect web          # полная инфа о контейнере
docker stats                # использование ресурсов live
docker diff web              # изменения в FS относительно image
docker events                 # поток событий демона
```

## Полезные флаги `docker run`

| Флаг | Значение |
|---|---|
| `-d` | detached (фон) |
| `-it` | интерактивный терминал |
| `--rm` | удалить контейнер после остановки |
| `-v host:container` | volume/bind mount |
| `-e VAR=value` | переменная окружения |
| `--restart unless-stopped` | автоперезапуск |
| `--network mynet` | подключить к сети |

#docker #containers #devops
