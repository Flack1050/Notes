---
tags: [linux, systemd, init]
---

# ⚙️ systemd

Связано с [[🧠Внутреннее устройство Linux]]

PID 1, юниты, таргеты (бывшие runlevels), логи.

## systemd — PID 1

**systemd** — первый процесс userspace (`PID 1`), родитель всех остальных процессов, управляет тем, в каком порядке и как стартуют сервисы.

**Unit** — базовая единица управления в systemd:

| Тип unit'а | Что описывает |
|---|---|
| `.service` | демон/сервис (nginx.service, docker.service) |
| `.socket` | сокет, который может сам стартовать сервис при первом подключении |
| `.mount` / `.automount` | точка монтирования |
| `.timer` | по расписанию (замена cron) |
| `.target` | группа unit'ов — точка синхронизации (аналог "уровня запуска") |

**Targets** — современная замена классических **runlevels** из SysV init:

| systemd target | старый runlevel | Что это |
|---|---|---|
| `poweroff.target` | 0 | выключение |
| `rescue.target` | 1 | однопользовательский режим (восстановление) |
| `multi-user.target` | 3 | многопользовательский, без графики (типичный сервер) |
| `graphical.target` | 5 | многопользовательский + графическое окружение |
| `reboot.target` | 6 | перезагрузка |

```bash
systemctl get-default              # какой target загружается по умолчанию
sudo systemctl set-default multi-user.target   # например, отключить графику при загрузке
systemctl isolate rescue.target      # переключиться на другой target прямо сейчас (аккуратно!)
```

## Управление сервисами (systemctl)

```bash
systemctl status nginx
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx          # применить конфиг без рестарта (если сервис поддерживает)
sudo systemctl enable nginx            # автозапуск при загрузке
sudo systemctl disable nginx
sudo systemctl enable --now nginx       # enable + start одной командой

systemctl list-units --type=service --state=running   # что сейчас запущено
systemctl list-unit-files --state=enabled                 # что в автозагрузке
systemctl --failed                                           # что упало
```

## Логи — journalctl

systemd пишет логи в бинарный **journal**, а не в плоские текстовые файлы `/var/log/*`:

```bash
journalctl -u nginx              # логи конкретного сервиса
journalctl -u nginx -f             # в реальном времени (follow)
journalctl -b                       # логи с текущей загрузки
journalctl -b -1                     # логи с предыдущей загрузки
journalctl --since "1 hour ago"
journalctl -p err                     # только ошибки и выше
journalctl --disk-usage               # сколько места занимает журнал
sudo journalctl --vacuum-size=200M     # обрезать журнал до размера
```

## Свой systemd-сервис

```ini
# /etc/systemd/system/myapp.service
[Unit]
Description=My App
After=network.target

[Service]
ExecStart=/usr/local/bin/myapp
Restart=on-failure
User=myapp

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload     # после создания/правки unit-файла
sudo systemctl enable --now myapp
```
