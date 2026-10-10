---
tags: [networks, devops, firewall, loadbalancing]
---

# 🧰 DevOps Networking

Связано: [[🌐Networks]] · [[🌐Nginx]] · [[🐳Docker]] · [[☸️Kubernetes]]

Прикладная сторона сетей — то, с чем реально сталкиваешься в работе: фаервол, балансировка, сеть в Docker/Kubernetes, диагностика.

## Фаервол: iptables / nftables / ufw

**iptables** — классика, работает через таблицы (filter, nat, mangle) и цепочки (INPUT/OUTPUT/FORWARD).

```bash
iptables -L -n -v                          # посмотреть правила
iptables -A INPUT -p tcp --dport 22 -j ACCEPT    # разрешить SSH
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -j DROP                   # дропнуть всё остальное (в конце!)
iptables-save > /etc/iptables/rules.v4       # сохранить правила
```

**nftables** — современная замена iptables (единый синтаксис вместо iptables/ip6tables/ebtables):

```bash
nft list ruleset
nft add rule inet filter input tcp dport 22 accept
```

**ufw** (Uncomplicated Firewall) — обёртка над iptables попроще, чаще на Ubuntu/Debian-серверах:

```bash
sudo ufw allow 22/tcp
sudo ufw allow from 192.168.1.0/24 to any port 5432
sudo ufw enable
sudo ufw status verbose
```

> [!note] Порядок правил важен
> Фаервол проверяет правила по порядку сверху вниз и останавливается на первом совпадении — поэтому широкое `DROP`/`deny` должно быть **последним**, иначе отрежешь себе доступ раньше нужных ACCEPT-правил.

## Reverse Proxy vs Load Balancer

- **Reverse Proxy** — стоит перед одним (или несколькими) backend'ом, прячет его реальный адрес, может терминировать TLS, кэшировать, добавлять заголовки. [[🌐Nginx]] — классический пример.
- **Load Balancer** — распределяет трафик между **несколькими одинаковыми** инстансами по алгоритму. Nginx `upstream` — это, по сути, L7-балансировщик; есть ещё L4-балансировщики (HAProxy, облачные NLB) — работают на уровне TCP-соединений, не разбирая HTTP.

### Алгоритмы балансировки

| Алгоритм | Суть |
|---|---|
| **Round-robin** | по кругу, по очереди на каждый сервер |
| **Least connections** | туда, где сейчас меньше всего активных соединений |
| **IP hash** | клиент всегда на один и тот же backend (sticky-сессии без общего storage) |
| **Weighted** | серверам назначается вес — на более мощный идёт больше трафика |

## Health checks

Балансировщик/оркестратор должен знать, жив ли backend, прежде чем слать туда трафик:

- **Liveness** — "процесс вообще жив?" (если нет — перезапустить).
- **Readiness** — "готов принимать трафик прямо сейчас?" (если нет — временно убрать из балансировки, не перезапускать).

В Kubernetes это ровно `livenessProbe` и `readinessProbe` (см. [[☸️Kubernetes]]).

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

В user-defined bridge-сети Docker поднимает встроенный DNS — контейнеры резолвят друг друга по имени контейнера/сервиса без ручного управления IP (именно поэтому в [[🧩Docker Compose]] сервисы обращаются друг к другу по имени из `docker-compose.yml`).

## Сеть в Kubernetes

- **CNI (Container Network Interface)** — плагин (Flannel/Calico/Cilium), который реально поднимает сеть между Pod'ами на разных нодах. Без CNI ноды остаются `NotReady` (см. [[☸️Kubernetes]] → kubeadm init).
- **Service** — стабильный виртуальный IP перед набором Pod'ов (у Pod'ов IP меняется при пересоздании):
  - `ClusterIP` (по умолчанию) — доступен только внутри кластера.
  - `NodePort` — открывает порт на каждой ноде (30000-32767), доступен снаружи напрямую.
  - `LoadBalancer` — просит внешний LB у облака (AWS ELB, GCP LB и т.д.).
- **Ingress** — L7-роутинг HTTP(S) снаружи внутрь кластера по host/path, обычно реализуется через ingress-nginx (см. [[🌐Nginx]]).
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

## VPN — коротко

Создаёт зашифрованный туннель между сетями/хостами поверх публичного интернета, как будто устройства находятся в одной локальной сети.

- **WireGuard** — современный, простой конфиг, быстрый (используется и для личного VPN, и для связи между серверами/дата-центрами).
- **OpenVPN** — старше, гибче в настройке, чуть тяжелее.

```bash
# пример WireGuard — минимальный конфиг на сервере
# /etc/wireguard/wg0.conf
[Interface]
PrivateKey = <server-private-key>
Address = 10.0.0.1/24
ListenPort = 51820

[Peer]
PublicKey = <client-public-key>
AllowedIPs = 10.0.0.2/32
```

## Инструменты диагностики

```bash
ping example.com                 # жив ли хост, базовая latency
traceroute example.com            # по каким узлам идёт пакет
mtr example.com                    # traceroute + ping в реальном времени, живой граф

curl -v https://example.com        # полный HTTP-запрос с заголовками
curl -I https://example.com         # только заголовки ответа (HEAD-подобно)

ss -tulnp                            # кто слушает какие порты
netstat -tulnp                        # то же самое, устаревший инструмент

tcpdump -i eth0 port 443              # снять трафик на интерфейсе (низкоуровневый дебаг)
nmap -p 1-1000 192.168.1.1              # просканировать открытые порты хоста

dig example.com
nslookup example.com
```

> [!tip] Частый сценарий "сайт не открывается"
> 1. `ping` — резолвится ли домен и отвечает ли хост вообще
> 2. `dig`/`nslookup` — тот ли IP отдаёт DNS
> 3. `curl -v` — доходит ли TCP/TLS handshake, что отвечает сервер
> 4. `ss -tulnp` на сервере — слушает ли приложение нужный порт
> 5. `iptables -L` / `ufw status` — не режет ли фаервол порт

#networks #devops #firewall #loadbalancing #docker-network #k8s-network
