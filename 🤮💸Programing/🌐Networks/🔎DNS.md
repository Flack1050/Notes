---
tags: [networks, dns]
---

# 🔎 DNS

Связано с [[🌐Networks]]

Как имя превращается в IP.

## DNS

Переводит доменные имена в IP. Иерархия: **Root → TLD (.com/.ru) → Authoritative NS (домена)**.

```
1. Браузер спрашивает локальный resolver (обычно у провайдера/роутера)
2. Resolver идёт в root-сервер → "кто отвечает за .com?"
3. Root отвечает адресом TLD-сервера .com
4. TLD-сервер отвечает адресом authoritative NS домена example.com
5. Authoritative NS отдаёт A-запись → реальный IP
6. Resolver кэширует ответ на TTL записи и отдаёт браузеру
```

### Основные типы записей

| Тип | Значение |
|---|---|
| **A** | домен → IPv4 |
| **AAAA** | домен → IPv6 |
| **CNAME** | домен → другой домен (алиас) |
| **MX** | почтовый сервер для домена |
| **TXT** | произвольный текст (часто для верификации, SPF/DKIM) |
| **NS** | какой сервер авторитативен для домена |

```bash
dig example.com              # полный DNS-запрос
dig +short example.com A
nslookup example.com
host example.com
```
