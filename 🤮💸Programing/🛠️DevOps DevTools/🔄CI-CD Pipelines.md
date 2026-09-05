---
tags: [cicd, pipelines, devops]
---

# 🔄 CI/CD Pipelines

Связано: [[🐳Docker]] · [[🌿Git Workflow]] · [[☸️Kubernetes]] · [[⚙️Ansible]] · [[🤵Jenkins]]

## Терминология

- **CI (Continuous Integration)** — автоматическая сборка + тесты при каждом коммите/PR.
- **CD (Continuous Delivery)** — автоматическая подготовка релиза (можно задеплоить по кнопке).
- **CD (Continuous Deployment)** — автоматический деплой в прод без ручного шага.
- **Pipeline / Workflow** — набор стадий (stages/jobs), выполняемых на **runner/agent**.
- **Artifact** — файл, переданный между стадиями (билд, отчёт о тестах, image).
- **Runner** — машина/контейнер, где реально исполняется job.

## Типичные стадии

```
lint → test → build → security-scan → push image → deploy (staging) → e2e tests → deploy (prod)
```

## GitHub Actions — пример

`.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm test

  build-and-push:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v6
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
```

## GitLab CI — пример

`.gitlab-ci.yml`:

```yaml
stages:
  - test
  - build
  - deploy

variables:
  IMAGE: registry.gitlab.com/group/project

test:
  stage: test
  image: node:20-alpine
  script:
    - npm ci
    - npm test

build:
  stage: build
  image: docker:24
  services: [docker:24-dind]
  script:
    - docker build -t $IMAGE:$CI_COMMIT_SHORT_SHA .
    - docker push $IMAGE:$CI_COMMIT_SHORT_SHA
  only: [main]

deploy:
  stage: deploy
  script:
    - ansible-playbook -i inventory/prod.ini deploy.yml
  only: [main]
  when: manual
```

## Стратегии деплоя

| Стратегия | Суть | Плюсы | Минусы |
|---|---|---|---|
| Recreate | стоп старой версии → старт новой | просто | downtime |
| Rolling update | постепенная замена реплик | нет полного даунтайма | смешение версий во время выката |
| Blue-Green | параллельно 2 окружения, переключение трафика | мгновенный откат | нужно 2x ресурсов |
| Canary | новая версия на % трафика | ранний фидбек | сложнее в настройке |

## Секреты в пайплайнах
- Никогда не хардкодь токены/пароли в yml — используй Secrets/Variables CI-системы.
- Ограничивай видимость секретов веткой/окружением (protected branches/environments).
- Ротация ключей + минимальные права токенов (principle of least privilege).

## Полезные практики
- Кэшируй зависимости (`node_modules`, `~/.cargo`, pip cache) — сильно ускоряет пайплайн.
- Разделяй pipeline на параллельные jobs там, где нет зависимостей.
- Фейль fast: линтер и юнит-тесты — самые быстрые проверки — должны идти первыми.
- Тегируй images по SHA коммита, а не только `latest`.

#cicd #pipelines #devops #github-actions #gitlab-ci
