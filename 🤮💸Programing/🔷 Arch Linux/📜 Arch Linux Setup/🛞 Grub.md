Связано с [[📋 Instructions.canvas|📋 Instructions]]

##                    Commands
**----------------------------------------------------------**

###                      Settings Grub
---
**1️⃣ Клонируем tokyo-night-grub**

```
git clone https://github.com/mino29/tokyo-night-grub.git
```

```
cd tokyo-night-grub
```


**2️⃣ Копируем тему в GRUB**

```
sudo mkdir -p /boot/grub/themes
```

```
sudo cp -r tokyo-night /boot/grub/themes/
```

**Проверь:**

```
ls /boot/grub/themes/
```
*Там должна быть папка tokyo-night.*


**3️⃣ Подключаем тему**

```
sudo micro /etc/default/grub
```

**Найди строку:**

```
#GRUB_THEME=
```

**И замени её на:**

```
GRUB_THEME="/boot/grub/themes/tokyo-night/theme.txt"
```


**4️⃣ Перегенерируем конфиг**

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```


**5️⃣ Перезагрузка**

```
sudo reboot
```

---

**----------------------------------------------------------**