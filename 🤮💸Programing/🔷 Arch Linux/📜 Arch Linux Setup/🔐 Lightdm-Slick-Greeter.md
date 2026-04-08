Связано с [[📋 Instructions.canvas|📋 Instructions]]

##                     Commands
**----------------------------------------------------------**

###        Settings Lightdm-Slick-Greeter
---
**1️⃣ Клонируем ghostly-greeter**

```
git clone https://github.com/mino29/ghostly-greeter.git
```

```
cd ghostly-greeter
```


**2️⃣  Копируем конфиги**

```
sudo cp -r lightdm/* /etc/lightdm/
```


**3️⃣ Проверяем greeter конфиг**

**Открой:**

```
sudo micro /etc/lightdm/slick-greeter.conf
```

**Убедись что там есть:**

```
[Greeter]
```


**4️⃣ Изменяем lightdm конфиг**

**Открой:**

```
sudo micro /etc/lightdm/lightdm.conf
```

**Надо найди в секции [Seat] вот это greeter-session:**

```
#greeter-session=example-gtk-gnome
```

**Надо изменить на:**

```
greeter-session=lightdm-slick-greeter
```


**5️⃣ Надо установить lightdm**

```
sudo pacman -S lightdm-slick-greeter 
```


**6️⃣ Перезагрузка**

```
sudo reboot
```

---

**----------------------------------------------------------**