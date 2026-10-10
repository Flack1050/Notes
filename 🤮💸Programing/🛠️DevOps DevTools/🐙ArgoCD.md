---
tags: [argocd, gitops, kubernetes, devops]
---

# 🐙 ArgoCD

Связано с [[🗺️ DevOps MOC]]


Декларативный GitOps-контроллер для Kubernetes: следит за git-репозиторием и держит кластер в состоянии, описанном там, автоматически подтягивая изменения.

## Установка

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# доступ к UI (для теста — port-forward, в проде — через Ingress)
kubectl port-forward svc/argocd-server -n argocd 8080:443

# логин: юзер admin, пароль лежит в секрете
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Ключевое понятие — Application

**Application** — CRD, который описывает: откуда брать манифесты (git repo + path/chart) и куда их катить (какой кластер + namespace).

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/org/myapp-config.git
    targetRevision: main
    path: environments/prod          # или chart: myapp + helm: {...}

  destination:
    server: https://kubernetes.default.svc
    namespace: prod

  syncPolicy:
    automated:
      prune: true        # удалять ресурсы, убранные из git
      selfHeal: true      # откатывать ручные kubectl-правки обратно к git
    syncOptions:
      - CreateNamespace=true
```

```bash
kubectl apply -f application.yaml -n argocd
```

## argocd CLI

```bash
argocd login localhost:8080

argocd app list
argocd app get myapp
argocd app sync myapp              # ручной sync (если automated выключен)
argocd app diff myapp               # что отличается между git и live
argocd app history myapp
argocd app rollback myapp <id>       # откат на предыдущую ревизию
argocd app delete myapp
```

## Sync-статусы

| Статус | Значение |
|---|---|
| **Synced** | live-состояние кластера совпадает с git |
| **OutOfSync** | в git есть изменения, ещё не применённые в кластер |
| **Healthy** | ресурсы (Deployment/Pod) реально живы и готовы |
| **Degraded** | ресурс применён, но не может подняться (CrashLoop и т.п.) |
| **Progressing** | rollout ещё идёт (например, rolling update Deployment) |

Sync ≠ Healthy: манифест может быть **Synced**, но приложение при этом **Degraded**, если контейнер падает.

## Helm-чарты как источник

```yaml
spec:
  source:
    repoURL: https://github.com/org/myapp-config.git
    path: charts/myapp
    helm:
      valueFiles:
        - values-prod.yaml
      parameters:
        - name: image.tag
          value: v1.4.1
```

## App of Apps — управление многими Application'ами

Вместо ручного `kubectl apply` под каждый сервис — один "родительский" Application, который сам создаёт остальные:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: root-app
  namespace: argocd
spec:
  source:
    repoURL: https://github.com/org/gitops-repo.git
    path: apps/           # тут лежат манифесты Application для каждого сервиса
  destination:
    server: https://kubernetes.default.svc
    namespace: argocd
  syncPolicy:
    automated: {}
```

Так один коммит с новым файлом в `apps/` разворачивает совершенно новый сервис без единого ручного `kubectl`.

## Projects — многокомандный доступ

**AppProject** ограничивает, куда и что конкретная команда/Application может деплоить (какие repo-источники разрешены, в какие namespace/кластеры, какие типы ресурсов) — полезно, когда ArgoCD общий на несколько команд.

## Webhook вместо polling

По умолчанию ArgoCD поллит git раз в ~3 минуты. Чтобы синхронизация была мгновенной — настраивается webhook из GitHub/GitLab на `https://<argocd-server>/api/webhook`, и ArgoCD реагирует сразу на push.

#argocd #gitops #kubernetes #devops
