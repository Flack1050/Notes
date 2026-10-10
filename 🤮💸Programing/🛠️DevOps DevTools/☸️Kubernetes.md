---
tags: [kubernetes, k8s, containers, devops]
---

# ☸️ Kubernetes

Связано с [[🗺️ DevOps MOC]]


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

## Как устроен кластер (компоненты)

**Control Plane** (обычно на master-ноде(ах)):
- **kube-apiserver** — единственная точка входа в кластер, все (включая другие компоненты) общаются только через него по REST/gRPC.
- **etcd** — распределённое key-value хранилище, единственный источник правды о состоянии кластера.
- **kube-scheduler** — решает, на какую ноду поставить новый Pod (по ресурсам, affinity, taints/tolerations).
- **kube-controller-manager** — гоняет контроллеры (Node, ReplicaSet, Deployment и т.д.), которые сводят actual state к desired state.
- **cloud-controller-manager** — (если кластер в облаке) интеграция с API облака (LB, диски, ноды).

**Node components** (на каждой ноде, включая master):
- **kubelet** — агент, который общается с apiserver, запускает/следит за контейнерами через container runtime, исполняет **static pods** из `/etc/kubernetes/manifests`.
- **kube-proxy** — настраивает iptables/IPVS-правила, чтобы Service-адреса резолвились в реальные Pod IP.
- **container runtime** (containerd/CRI-O) — реально стартует контейнеры по команде kubelet через CRI.

> [!note] Важный факт
> Control plane компоненты (apiserver, etcd, scheduler, controller-manager) в kubeadm-кластере — это **static pods**, которыми управляет локальный kubelet, а не kube-scheduler. Именно поэтому кластер вообще может подняться до того, как control plane заработал — kubelet не ждёт apiserver, чтобы запустить его же самого.

## kubeadm init — что происходит под капотом

Когда ты запускаешь `kubeadm init` на будущей master-ноде, по шагам:

1. **Preflight checks** — проверка версии ОС, swap (должен быть выключен), портов (6443, 2379-2380, 10250 и т.д.), наличия container runtime.
2. **Генерация CA и сертификатов** — kubeadm создаёт корневой CA (`/etc/kubernetes/pki/ca.crt`) и сертификаты для apiserver, etcd, kubelet client cert и т.д.
3. **Генерация kubeconfig-файлов** — `admin.conf`, `kubelet.conf`, `controller-manager.conf`, `scheduler.conf` — каждый со своими правами доступа к apiserver.
4. **Генерация static pod манифестов** для etcd, kube-apiserver, kube-controller-manager, kube-scheduler → кладутся в `/etc/kubernetes/manifests/`.
5. **kubelet подхватывает манифесты** — он постоянно следит за этой папкой и сразу поднимает описанные static pods: сначала **etcd**, затем **kube-apiserver** (подключается к etcd), затем **scheduler** и **controller-manager** (подключаются к apiserver).
6. **kubeadm ждёт**, пока `kube-apiserver` не станет отвечать на `/healthz` — только после этого продолжает.
7. **Загрузка kubeadm-config** в ConfigMap `kube-system/kubeadm-config` — чтобы `kubeadm join` и будущие `kubeadm upgrade` знали конфигурацию кластера.
8. **Bootstrap Token + RBAC** — создаётся токен для `kubeadm join` и RBAC-правила для авто-подписи CSR от новых нод.
9. **Установка kube-proxy** (DaemonSet) и **CoreDNS** (Deployment) через apiserver — но их Pod'ы повиснут в `Pending`/`ContainerCreating`, пока не поставлен **CNI-плагин** (сеть).
10. **Taint на control-plane ноду** (`node-role.kubernetes.io/control-plane:NoSchedule`) — чтобы обычные workload'ы туда не планировались.
11. В конце — вывод команды для `kubeadm join` (с токеном и discovery-token-ca-cert-hash) для остальных нод.

### Что делать сразу после `kubeadm init`

```bash
# скопировать kubeconfig, чтобы kubectl заработал от твоего юзера
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# поставить CNI — без этого ноды останутся NotReady
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
# (или Calico/Cilium — выбор CNI влияет на NetworkPolicy и производительность)

# на воркерах — команда, которую вывел kubeadm init
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## Порядок загрузки кластера целиком (boot order)

```
1. kubelet стартует на master-ноде (как системный сервис, ещё до кластера)
2. kubelet видит манифесты в /etc/kubernetes/manifests → поднимает static pods:
   etcd → kube-apiserver → kube-scheduler → kube-controller-manager
3. kube-apiserver отвечает на /healthz → control plane считается живым
4. kubeadm накатывает kube-proxy (DaemonSet) и CoreDNS (Deployment) через apiserver
   → они запланированы, но Pending, т.к. ноды ещё NotReady (нет сети)
5. kubectl apply -f <cni>.yaml → CNI-плагин разворачивается как DaemonSet
6. CNI на ноде поднимает сетевой интерфейс → kubelet помечает ноду Ready
7. Планировщик наконец может разместить kube-proxy и CoreDNS Pod'ы → они запускаются
8. kubeadm join на воркерах → их kubelet получает сертификат (через bootstrap token),
   регистрируется в apiserver как Node, тоже становится Ready после CNI
9. Кластер полностью рабочий: можно катить обычные Deployment/Service
```

## Локальный кластер для разработки

```bash
# k3d (легковесный k8s в докере) — удобно на Arch
yay -S k3d
k3d cluster create dev

# либо minikube
minikube start
```

## Helm

Пакетный менеджер для Kubernetes — подробный разбор чартов, values и команд вынесен в отдельную заметку: ⛵Helm

> [!note] Когда нужен k8s, а когда хватит Compose
> Если у тебя один сервер и нагрузка не требует горизонтального авто-масштабирования — 🧩Docker Compose проще и достаточно. K8s стоит подключать, когда нужен self-healing, multi-node scheduling или zero-downtime rolling updates на масштабе.

#kubernetes #k8s #containers #devops
