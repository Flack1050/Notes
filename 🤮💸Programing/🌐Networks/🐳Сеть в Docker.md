---
tags: [networks, docker]
---

# 🐳 Сеть в Docker

Связано с [[🌐Networks]]

Драйверы bridge/host/overlay и DNS между контейнерами.

## Сеть в Docker

```bash
docker network ls
docker network create mynet
docker run --network mynet --name app1 ...
```

| Драйвер | Когда |
|---|---|
| **bridge** (по умолчанию) | изолированная виртуальная сеть на хосте, контейнеры видят друг друга по имени (если в одной user-defined bridge-сети) |
| **host** | контейнер использует сетевой стек хоста напрямую — нет изоляции портов, максимальная скорость |
| **none** | без сети вообще |
| **overlay** | сеть между контейнерами на **разных** хостах (Docker Swarm) |

В user-defined bridge-сети Docker поднимает встроенный DNS — контейнеры резолвят друг друга по имени контейнера/сервиса без ручного управления IP (именно поэтому в 🧩Docker Compose сервисы обращаются друг к другу по имени из `docker-compose.yml`).
