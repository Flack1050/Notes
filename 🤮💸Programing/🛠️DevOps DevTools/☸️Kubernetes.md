---
tags: [kubernetes, k8s, containers, devops]
---

# ☸️ Kubernetes

Связано: [[🐳Docker]] · [[🔄CI-CD Pipelines]] · [[🏗️Terraform]]

Оркестратор контейнеров: сам следит, чтобы описанное состояние ("desired state") соответствовало реальному ("actual state").

## Базовые объекты

| Объект | Назначение |
|---|---|
| **Pod** | минимальная единица — 1+ контейнеров с общей сетью/volume |
| **Deployment** | управляет ReplicaSet'ами → декларативные апдейты Pod'ов |
| **Service** | стабильный сетевой адрес для набора Pod'ов |
| **ConfigMap / Secret** | конфигурация / чувствительные данные |
| **Ingress** | HTTP(S) роутинг снаружи в кластер |
| **StatefulSet** | как Deployment, но для stateful-приложений (стабильные имена, volumes) |
| **Namespace** | логическая изоляция ресурсов внутри кластера |

## Пример Deployment + Service

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels: { app: myapp }
  template:
    metadata:
      labels: { app: myapp }
    spec:
      containers:
        - name: myapp
          image: ghcr.io/org/myapp:1.4.0
          ports: [{ containerPort: 8080 }]
          resources:
            requests: { cpu: "100m", memory: "128Mi" }
            limits: { cpu: "500m", memory: "256Mi" }
          livenessProbe:
            httpGet: { path: /healthz, port: 8080 }
            initialDelaySeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector: { app: myapp }
  ports:
    - port: 80
      targetPort: 8080
  type: ClusterIP
```

## kubectl — основные команды

```bash
kubectl get pods -n myns
kubectl get all -n myns
kubectl describe pod mypod -n myns
kubectl logs -f mypod -n myns
kubectl exec -it mypod -n myns -- sh

kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl rollout status deployment/myapp
kubectl rollout undo deployment/myapp
kubectl scale deployment/myapp --replicas=5

kubectl config get-contexts
kubectl config use-context prod-cluster
```

## Локальный кластер для разработки

```bash
# k3d (легковесный k8s в докере) — удобно на Arch
yay -S k3d
k3d cluster create dev

# либо minikube
minikube start
```

## Helm (пакетный менеджер k8s)

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-redis bitnami/redis
helm upgrade my-redis bitnami/redis --set replica.replicaCount=2
helm uninstall my-redis
```

> [!note] Когда нужен k8s, а когда хватит Compose
> Если у тебя один сервер и нагрузка не требует горизонтального авто-масштабирования — [[🧩Docker Compose]] проще и достаточно. K8s стоит подключать, когда нужен self-healing, multi-node scheduling или zero-downtime rolling updates на масштабе.

#kubernetes #k8s #containers #devops
