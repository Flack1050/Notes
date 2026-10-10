---
tags: [networks, kubernetes, cni]
---

# ☸️ Сеть в Kubernetes

Связано с [[🌐Networks]]

CNI, Service, Ingress, NetworkPolicy.

## Сеть в Kubernetes

- **CNI (Container Network Interface)** — плагин (Flannel/Calico/Cilium), который реально поднимает сеть между Pod'ами на разных нодах. Без CNI ноды остаются `NotReady` (см. ☸️Kubernetes → kubeadm init).
- **Service** — стабильный виртуальный IP перед набором Pod'ов (у Pod'ов IP меняется при пересоздании):
  - `ClusterIP` (по умолчанию) — доступен только внутри кластера.
  - `NodePort` — открывает порт на каждой ноде (30000-32767), доступен снаружи напрямую.
  - `LoadBalancer` — просит внешний LB у облака (AWS ELB, GCP LB и т.д.).
- **Ingress** — L7-роутинг HTTP(S) снаружи внутрь кластера по host/path, обычно реализуется через ingress-nginx (см. 🌐Nginx).
- **NetworkPolicy** — "фаервол" между Pod'ами: по умолчанию в Kubernetes любой Pod может достучаться до любого, NetworkPolicy явно ограничивает это (кто к кому может ходить).

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-from-other-namespaces
spec:
  podSelector: {}
  ingress:
    - from:
        - podSelector: {}   # разрешить только из того же namespace
```
