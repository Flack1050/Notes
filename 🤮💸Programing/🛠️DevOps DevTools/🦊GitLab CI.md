---
tags: [gitlab-ci, cicd, gitops, devops]
---

# 🦊 GitLab CI

Связано: [[🔄CI-CD Pipelines]] · [[🔁GitOps]] · [[🐙ArgoCD]] · [[📘Git]]

Встроенная в GitLab CI/CD-система: пайплайн описывается в `.gitlab-ci.yml` в корне репозитория, выполняется на **GitLab Runner**'ах. Базовый пример пайплайна уже есть в [[🔄CI-CD Pipelines]] — здесь глубже про синтаксис и продвинутые фичи.

## Структура `.gitlab-ci.yml`

```yaml
stages:
  - test
  - build
  - deploy

variables:
  IMAGE: registry.gitlab.com/group/project

default:
  image: alpine:3.20        # образ по умолчанию для всех job, если не переопределён
  tags: [docker]              # какой Runner брать (по тегам)

test:
  stage: test
  image: node:20-alpine
  script:
    - npm ci
    - npm test
```

- **stage** — к какой стадии относится job (job'ы одной стадии выполняются параллельно, стадии — последовательно).
- **script** — команды, которые реально выполняются.
- **image** — Docker-образ, в котором крутится job (GitLab Runner с executor `docker`).

## rules — когда запускать job (современный способ вместо only/except)

```yaml
deploy_prod:
  stage: deploy
  script: ./deploy.sh prod
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: manual              # ручной запуск кнопкой в UI
    - if: '$CI_COMMIT_TAG'
      when: on_success           # авто-запуск на любой git tag
    - when: never                 # во всех остальных случаях — не запускать

deploy_review:
  stage: deploy
  script: ./deploy.sh review
  rules:
    - if: '$CI_MERGE_REQUEST_ID'   # только в контексте merge request
```

`only`/`except` — устаревший синтаксис, `rules` гибче (несколько условий, `when`, `changes`).

## changes — запускать job только если поменялись нужные файлы

```yaml
build_backend:
  script: docker build -t backend backend/
  rules:
    - changes:
        - backend/**/*
```

Экономит время в монорепо — не пересобирать всё на любой коммит.

## Кэш и артефакты

```yaml
test:
  stage: test
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
    policy: pull-push
  script:
    - npm ci
    - npm test
  artifacts:
    when: always
    paths:
      - coverage/
    reports:
      junit: junit.xml
    expire_in: 1 week
```

- **cache** — переиспользуется между запусками пайплайна (ускоряет `npm ci`/`pip install`), но не гарантированно (best-effort).
- **artifacts** — файлы, передаваемые между job'ами разных стадий (например, собранный билд из `build` в `deploy`), плюс видны в UI GitLab (отчёты о тестах, coverage).

## Переменные и секреты

```
Settings → CI/CD → Variables
```

- **Protected** — переменная доступна только в pipeline на protected-ветках/тегах (обычно `main`).
- **Masked** — значение скрывается в логах job'а.

```yaml
deploy:
  script:
    - echo "Deploying with token ${DEPLOY_TOKEN}"   # значение из Settings, не хардкод
```

Встроенные предопределённые переменные, которые часто нужны: `$CI_COMMIT_SHA`, `$CI_COMMIT_SHORT_SHA`, `$CI_COMMIT_BRANCH`, `$CI_COMMIT_TAG`, `$CI_PROJECT_NAME`, `$CI_MERGE_REQUEST_ID`, `$CI_PIPELINE_SOURCE`.

## Environments и Review Apps

```yaml
deploy_review:
  stage: deploy
  script: ./deploy.sh $CI_ENVIRONMENT_SLUG
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_ENVIRONMENT_SLUG.review.example.com
    on_stop: stop_review
  rules:
    - if: '$CI_MERGE_REQUEST_ID'

stop_review:
  stage: deploy
  script: ./teardown.sh $CI_ENVIRONMENT_SLUG
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    action: stop
  when: manual
```

Каждый MR получает своё изолированное окружение с уникальным URL, которое можно посмотреть до мержа, и которое само удаляется при закрытии MR.

## include — переиспользование конфигов между репозиториями

```yaml
include:
  - project: 'group/ci-templates'
    file: 'templates/docker-build.yml'
  - local: '.gitlab/lint.yml'
  - remote: 'https://example.com/shared-ci.yml'
```

Позволяет держать общий пайплайн-шаблон в одном месте и подключать его во всех проектах организации — не копипастить один и тот же `.gitlab-ci.yml`.

## GitLab Runner

**Runner** — агент, который реально исполняет job'ы. Может быть shared (от самого GitLab.com) или self-hosted.

```bash
# регистрация self-hosted runner
sudo gitlab-runner register \
  --url https://gitlab.com/ \
  --registration-token <token> \
  --executor docker \
  --docker-image alpine:3.20
```

**Executor** — как именно раннер запускает job: `docker` (каждый job — новый контейнер, самый частый выбор), `shell` (прямо на хосте раннера), `kubernetes` (job = Pod в кластере).

## GitLab CI как часть GitOps-флоу

GitLab CI сам по себе — push-модель, но его часто используют именно как **тот, кто обновляет config-репозиторий** для [[🐙ArgoCD]]/Flux (см. [[🔁GitOps]]):

```yaml
update_gitops_repo:
  stage: deploy
  script:
    - git clone https://oauth2:${GITOPS_TOKEN}@gitlab.com/org/gitops-repo.git
    - cd gitops-repo
    - yq -i ".image.tag = \"${CI_COMMIT_SHORT_SHA}\"" environments/prod/values.yaml
    - git commit -am "bump image to ${CI_COMMIT_SHORT_SHA}"
    - git push
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

Дальше ArgoCD сам увидит изменение в `gitops-repo` и применит его в кластер — GitLab CI здесь ничего не деплоит напрямую.

## Debugging пайплайна

```bash
# локальный прогон одного job через gitlab-runner (без пуша)
gitlab-runner exec docker test
```

- Кнопка **Retry** в UI — перезапустить упавший job без нового коммита.
- `CI_DEBUG_TRACE: "true"` (в variables) — подробный трейс каждой команды в логе job'а.

#gitlab-ci #cicd #gitops #devops
