Связано с [[🗺️ Arch Linux Navigation.canvas|🗺️ Arch Linux Navigation]]

##         🆘 Arch Troubleshooting
**----------------------------------------------------------**

---
###      🔎 1. Система не загружается

**Что делать:** посмотреть логи прошлой загрузки и проверить initramfs/ядро.

- Просмотр логов прошлой загрузки:

``` bash
journalctl -b -1
```

- Если загрузка текущая — логи текущей:

``` bash
journalctl -b
```

- Проверить версию ядра:

``` bash
uname -r
```

- Пересобрать initramfs (если ядро/модули менялись):

``` bash
sudo mkinitcpio -P
```

- Recovery (если система не грузится вообще): загрузиться с Arch ISO смонтировать разделы и:

``` bash
arch-chroot /mnt
# дальше работать как в обычной системе
```

---
###   🌐 2. Нет интернета / сеть падает

**Проверки в порядке:**

- Есть IP?

``` bash
ip a
```

- Статус NetworkManager:

``` bash
systemctl status NetworkManager
# если не активен
sudo systemctl start NetworkManager
```

- Посмотреть сетевые юниты (возможный конфликт NM vs systemd-networkd):

``` bash
systemctl list-units --type=service | grep -i network
```

- Проверить доступность IP и DNS:

``` bash
ping -c 3 8.8.8.8
ping -c 3 google.com
```

(если IP пингуется а домен — нет → проблема в DNS)

- Быстрая полезная проверка интерфейсов:

``` bash
ip route
nmcli device status
```

---
###           🔥 3. Permission denied                               (ошибки доступа)

**Диагностика и решение:**

- Смотрим права и владельца:

``` bash
ls -l path/to/file
```

- Исправить владельца/права:

``` bash
sudo chown user:group file
chmod 755 file      # пример
chmod +x script.sh  # дать право на исполнение
```

> Часто проблема — просто забыли `chmod +x` или поставили неверного владельца.

---
###           🧊 4. Система тормозит                               (CPU / RAM / I/O)

**Проверки:**

- Процессы:

``` bash
top
# или удобнее если установлен:
htop
```

- Память:

``` bash
free -h
```

- Диск (заполненность):

``` bash
df -h
```

- I/O (если подозрение на диск):

``` bash
# iotop (установить при необходимости)
sudo iotop
```

> Если диск почти полон — многие сервисы падают или ведут себя странно. Проверяй `df -h` первым делом.

---
###       🧨 5. Служба не запускается

**Шаги:**

1. Статус службы:

``` bash
systemctl status service_name
```

2. Смотреть логи службы (детально):

``` bash
journalctl -u service_name -xe
# или в реальном времени
journalctl -u service_name -f
```

3. Если меняли unit-файл — перезагрузить конфиги systemd:

``` bash
sudo systemctl daemon-reload
```

4. Запустить вручную и посмотреть ошибки:

``` bash
sudo systemctl start service_name
sudo systemctl status service_name
```

> Обычно `journalctl -u ... -xe` показывает причину напрямую.

---
###            📦 6. Проблемы после                     обновления (rolling release)

**Нюансы и быстрые шаги:**

- Посмотреть последние обновления в логах pacman:

``` bash
grep upgraded /var/log/pacman.log
```

- Есть баг после конкретного пакета — можно откатить (downgrade) из кеша:

``` bash
ls /var/cache/pacman/pkg/
sudo pacman -U /var/cache/pacman/pkg/package-version.pkg.tar.zst
```

- Если сломался пакетный менеджер или база — аккуратно:  
    сначала резерв (не трогай без понимания), потом `pacman -Syu` или восстановление из chroot.
    

> Совет: держи свежий бэкап `pacman -Qe > pkglist.txt` перед крупными обновлениями.

---
###       🖥 7. Xorg / Wayland / Display                     manager не запускается

**Проверки:**

- Статус дисплей-менеджера:

``` bash
systemctl status display-manager
```

- Логи X/Wayland: обычно в `~/.local/share/xorg/` или `journalctl` (если dm использует journal).
    
- Проверить видеодрайверы:

``` bash
lsmod | grep -E 'nvidia|amdgpu|i915'
```

- При подозрении на драйвер — перегенерировать initramfs и перезагрузиться.

---
###               🔐 8. Подозрение на                        взлом / компрометацию

**Что быстро проверить:**

- Список пользователей:

``` bash
cat /etc/passwd
```

- Последние логины:

``` bash
last
```

- Открытые порты:

``` bash
ss -tuln
```

- Проверить sudo привилегии:

``` bash
sudo -l
```

- Логи SSH:

``` bash
journalctl -u sshd
```

**Ищи:** всплески неудачных попыток логины с чужих IP незнакомые аккаунты неожиданные процессы.

> Если есть признаки взлома — изолируй сервер (отключи сеть) собери логи, делай форензик и восстанавливай из доверенных бэкапов.

---
###    🧾 Полезные команды и трюки                             (сборник)

- Показать последние 50 строк журнала:

``` bash
journalctl -n 50
```

- Логи в реальном времени:

``` bash
journalctl -f
```

- Показать кто слушает порты:

``` bash
ss -tuln
```

- Показать орфаны pacman:

``` bash
pacman -Qtdq
```

- Удалить сироты:

``` bash
sudo pacman -Rns $(pacman -Qtdq)
```

- Проверить, что недавно было обновлено:

``` bash
tail -n 50 /var/log/pacman.log
```

---
###   🧰 Чек-лист «если совсем плохо»                      (Recovery plan)

1. Сохрани логи (`journalctl > /root/journal.log`) и снимки состояния.
    
2. Если не грузится → загрузиться с Arch ISO → смонтировать и `arch-chroot /mnt`.
    
3. В chroot: `pacman -Syu`, `mkinitcpio -P`, проверка `grub-install`/конфигурации загрузчика.
    
4. Откат пакетов из `/var/cache/pacman/pkg/`, если известен виновный пакет.
    
5. Восстановление из бэкапа — быстрее и безопаснее чем час дебага без гарантии.

---

**----------------------------------------------------------**