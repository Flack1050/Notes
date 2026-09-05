Связано с [[🤮💸Programing/🐧Linux/🔷 Arch Linux 1/🗺️ Arch Linux Navigation.canvas|🗺️ Arch Linux Navigation]]

##   🌐 Arch Network Management
**----------------------------------------------------------**

---
###           🌐 1. Просмотр сетевых                                   интерфейсов

``` bash
ip addr show  
ip link
```

| **Команда** |    **Значение**     |
| :---------: | :-----------------: |
|   ip addr   | Показать IP адреса  |
|   ip link   | Показать интерфейсы |
|             |                     |

---
###     🔌 2. Ethernet (проводная сеть)

**Проверка статуса:**

``` bash
ip link
```

**Включить интерфейс:**

``` bash
sudo ip link set enp3s0 up
```

*💡 Обычно NetworkManager делает это автоматически.*

---
###        📡 3. Wi-Fi через iwctl (iwd)

**Запуск:**

``` bash
iwctl
```

**Внутри iwctl:**

``` bash
device list  
station wlan0 scan  
station wlan0 get-networks  
station wlan0 connect WIFI_NAME
```

---
###              🖥 4. NetworkManager                   (рекомендуется для Desktop)

**правление**:

``` bash
sudo systemctl enable NetworkManager  
sudo systemctl start NetworkManager
```

**CLI управление:**

``` bash
nmcli device status  
nmcli device wifi list  
nmcli device wifi connect "SSID" password "PASSWORD"
```

---
###      📶 5. Проверка подключения

``` bash
ping archlinux.org
```

---
###                       🔐 6. SSH

``` bash
ssh user@host
```

**Установить сервер:**

``` bash
sudo pacman -S openssh  
sudo systemctl enable sshd  
sudo systemctl start sshd
```

---
###                    🔥 7. Firewall

**UFW (простой вариант):**

``` bash
sudo pacman -S ufw  
sudo ufw enable  
sudo ufw status
```

---

**----------------------------------------------------------**