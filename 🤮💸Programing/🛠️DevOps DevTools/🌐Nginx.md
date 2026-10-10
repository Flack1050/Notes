---
tags: [nginx, devops, networking]
---

# 🌐 Nginx

Связано: [[🐳Docker]] · [[☸️Kubernetes]] · [[🔄CI-CD Pipelines]] · [[🌐Networks]]

Веб-сервер и обратный прокси. В DevOps чаще всего используется как: reverse proxy перед приложением, балансировщик нагрузки, TLS-терминатор, раздача статики.

## Установка и базовые команды (Arch)

```bash
sudo pacman -S nginx
sudo systemctl enable --now nginx

nginx -t                 # проверить конфиг на синтаксис
sudo systemctl reload nginx    # применить конфиг без разрыва соединений
sudo systemctl restart nginx
journalctl -u nginx -f
```

## Структура конфига

```
/etc/nginx/
├── nginx.conf              # главный конфиг, обычно только include'ы
├── sites-available/         # конфиги виртуальных хостов (не подключены)
├── sites-enabled/            # симлинки на активные из sites-available
├── conf.d/                    # доп. конфиги (часто через *.conf)
└── snippets/                  # переиспользуемые куски (ssl-params, headers)
```

```nginx
# nginx.conf — минимальный скелет
user www-data;
worker_processes auto;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    include /etc/nginx/sites-enabled/*;
}
```

## Reverse proxy — базовый пример

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## Балансировка нагрузки (upstream)

```nginx
upstream backend {
    least_conn;                       # least_conn / ip_hash / round-robin (по умолчанию)
    server 10.0.0.1:3000;
    server 10.0.0.2:3000;
    server 10.0.0.3:3000 backup;      # включится, только если остальные упали
}

server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```

## HTTPS / TLS (с Let's Encrypt)

```bash
sudo pacman -S certbot certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```

```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate     /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://backend;
    }
}

server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;   # редирект http → https
}
```

## Раздача статики + SPA (React/Vue)

```nginx
server {
    listen 80;
    root /var/www/myapp/dist;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;   # чтобы работал client-side роутинг
    }

    location /assets/ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
}
```

## Nginx как Ingress Controller в Kubernetes

В связке с [[☸️Kubernetes]] чаще всего ставится не сам nginx, а **ingress-nginx** (отдельный чарт [[⛵Helm]]):

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx --create-namespace
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: myapp
                port:
                  number: 80
```

## Nginx в Docker

```dockerfile
FROM nginx:1.27-alpine
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY dist/ /usr/share/nginx/html/
```

## Полезные директивы

| Директива | Что делает |
|---|---|
| `gzip on;` | сжатие ответов |
| `client_max_body_size 20M;` | лимит на размер тела запроса (для аплоадов) |
| `limit_req_zone` / `limit_req` | rate limiting по IP |
| `add_header X-Frame-Options DENY;` | базовые security-заголовки |
| `error_page 502 503 504 /50x.html;` | кастомная страница ошибки |

## Отладка

```bash
nginx -T                        # вывести весь итоговый конфиг (со всеми include)
tail -f /var/log/nginx/access.log
tail -f /var/log/nginx/error.log
```

#nginx #devops #networking #reverse-proxy
