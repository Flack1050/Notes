---
tags: [linux, boot, systemd, init]
---

# 🥾 Загрузка и init

Связано: [[🧠Архитектура Linux]] · [[⚙️Процессы, память и файловая система]]

Путь от нажатия кнопки питания до рабочего шелла/логина.

## Полная цепочка загрузки

```
1. BIOS/UEFI       — проверка железа (POST), поиск загрузочного устройства
2. Bootloader      — GRUB (или systemd-boot) показывает меню, грузит ядро + initramfs
3. Kernel init      — ядро распаковывается, инициализирует память, CPU, базовые драйверы
4. initramfs          — временный корень в RAM: подгружает модули (диск/FS), чтобы смонтировать реальный "/"
5. pivot_root/switch_root — переключение с initramfs на настоящую корневую ФС с диска
6. PID 1 (systemd)     — первый процесс userspace, запускает всё остальное
7. Targets/units        — systemd поднимает сеть, логирование, графику, твои сервисы
8. Login / display manager — getty (TTY) или SDDM/GDM (графический вход)
```

### 1-2. BIOS/UEFI → Bootloader

- **BIOS** (legacy) читает **MBR** (первые 446 байт диска) — там лежит код загрузчика первой стадии.
- **UEFI** (современный стандарт) читает **EFI System Partition** (FAT32-раздел с `.efi` файлами) — более гибкий, поддерживает Secure Boot, GPT-диски >2ТБ.

**GRUB** (GRand Unified Bootloader) — показывает меню выбора ОС/ядра, передаёт ядру параметры загрузки (`/etc/default/grub` → `grub-mkconfig`):

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg   # пересобрать конфиг grub (Arch)
cat /proc/cmdline                             # с какими параметрами реально загрузилось текущее ядро
```

### 3-5. Ядро и initramfs

**initramfs** — временная корневая файловая система **в оперативной памяти**, которую ядро монтирует первой. Нужна потому, что для монтирования реального диска часто нужны модули (драйвер RAID, LVM, шифрование диска, NVMe), которые ещё не загружены — initramfs содержит минимальный набор инструментов и модулей именно для этого.

```bash
mkinitcpio -P                   # пересобрать initramfs (Arch Linux)
lsinitcpio /boot/initramfs-linux.img   # посмотреть что внутри
```

После того как настоящий диск смонтирован, ядро делает `switch_root`/`pivot_root` — переключается с временного initramfs-корня на реальный `/` с диска, и запускает **PID 1**.

### 6-7. systemd — PID 1

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

#linux #boot #systemd #init #grub
