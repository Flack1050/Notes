---
tags: [helm, kubernetes, k8s, devops]
---

# ⛵ Helm

Связано: [[☸️Kubernetes]] · [[CI-CD Pipelines|🔄CI-CD Pipelines]]

Пакетный менеджер для Kubernetes — вместо десятков YAML-манифестов с ручными правками ты ставишь/обновляешь приложение одной командой из шаблонизированного пакета (**chart**).

## Ключевые понятия

- **Chart** — пакет с шаблонами Kubernetes-манифестов + метаданные (`Chart.yaml`) + дефолтные значения (`values.yaml`).
- **Release** — конкретный установленный в кластер экземпляр чарта (у одного чарта может быть много релизов с разными именами/namespace).
- **Values** — переменные, которыми параметризуются шаблоны чарта (образ, реплики, ресурсы и т.д.).
- **Repository (repo)** — HTTP(S)-хранилище чартов (Bitnami, ingress-nginx, свой Harbor/ChartMuseum).
- **Template** — Go-template `.yaml` файл, который Helm рендерит в реальный манифест перед `kubectl apply`.

## Структура чарта

```
mychart/
├── Chart.yaml           # имя, версия чарта, версия приложения
├── values.yaml           # значения по умолчанию
├── charts/                # вложенные (sub-)чарты — зависимости
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl      # общие шаблон-функции (define/include)
│   └── NOTES.txt          # текст, который выводится после установки
└── .helmignore
```

## Основные команды

```bash
# репозитории
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo redis

# установка / обновление
helm install my-redis bitnami/redis
helm install my-redis bitnami/redis -f my-values.yaml
helm install my-redis bitnami/redis --set replica.replicaCount=2 --namespace cache --create-namespace
helm upgrade my-redis bitnami/redis --set replica.replicaCount=3
helm upgrade --install my-redis bitnami/redis   # install если релиза ещё нет, иначе upgrade

# просмотр состояния
helm list -A                     # все релизы во всех namespace
helm status my-redis
helm get values my-redis         # какие values реально применены
helm show values bitnami/redis   # дефолтные values чарта (до установки)
helm history my-redis            # история ревизий релиза

# откат и удаление
helm rollback my-redis 2         # откатить на ревизию 2
helm uninstall my-redis

# отладка шаблонов
helm template mychart/                    # отрендерить манифесты локально, без apply
helm install my-redis bitnami/redis --dry-run --debug
helm lint mychart/                         # проверка чарта на ошибки

# создание своего чарта
helm create mychart
```

## values.yaml — пример

```yaml
replicaCount: 2

image:
  repository: myorg/myapp
  tag: "1.4.0"
  pullPolicy: IfNotPresent

resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 256Mi

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: true
  host: myapp.example.com
```

## Пример шаблона (templates/deployment.yaml)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-myapp
  labels:
    app: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
        - name: myapp
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
```

- `{{ .Release.Name }}` — имя релиза (то, что указал после `helm install <name>`).
- `{{ .Values.xxx }}` — значение из `values.yaml` (или переопределённое через `-f`/`--set`).
- `{{- toYaml ... | nindent N }}` — частый паттерн: вставить произвольный блок YAML с нужным отступом.

## Несколько окружений (dev/stage/prod)

Обычно держат общий `values.yaml` + оверрайды на окружение:

```bash
helm upgrade --install myapp ./mychart \
  -f values.yaml \
  -f values.prod.yaml \
  --namespace prod
```

`values.prod.yaml` переопределяет только то, что отличается (реплики, домен, ресурсы) — Helm мержит values по порядку файлов (последний побеждает).

## Helm в CI/CD

Типичный шаг деплоя в pipeline (см. [[CI-CD Pipelines|🔄CI-CD Pipelines]]):

```bash
helm upgrade --install myapp ./chart \
  --namespace prod \
  --set image.tag=$CI_COMMIT_SHORT_SHA \
  --wait --timeout 5m
```

`--wait` заставит Helm дождаться, пока Pod'ы реально станут Ready, а не просто отдать манифесты в apiserver — полезно, чтобы pipeline упал, если деплой не поднялся.

> [!tip] Helm vs голые манифесты vs Kustomize
> Голые `kubectl apply -f` — просто, но параметризация только через sed/envsubst-костыли.
> **Kustomize** — оверлеи без шаблонов (patch поверх base), встроен в kubectl (`kubectl apply -k`).
> **Helm** — полноценный шаблонизатор + версионирование релизов + rollback из коробки — выигрывает, когда приложение ставят повторно с разными values (свой продукт, чужие чарты из Bitnami/ingress-nginx и т.д.).

#helm #kubernetes #k8s #devops
