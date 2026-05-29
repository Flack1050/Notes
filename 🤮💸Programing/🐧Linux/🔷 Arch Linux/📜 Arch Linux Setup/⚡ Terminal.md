Связано с [[📋 Setup Instruction.canvas|📋 Instructions]]

##                     Commands
**----------------------------------------------------------**

###                   Settings Terminal
---
**1️⃣ Устанавливаем Kitty и Fish**

```
sudo pacman -S kitty
```

```
sudo pacman -S fish
```


**2️⃣  Настраиваем конфиг Kitty**

**Открой:**

```
sudo micro /home/user/.config/kitty/kitty.conf
```

**Замены все на вот это:**

```
font_family JetBrains Mono font_size 11.0

include themes/catppuccin.conf
background_opacity 0.90

# Background
background #1e1e2e
foreground #cdd6f4

# Cursor
cursor #f5e0dc

# Colors
color0  #45475a
color1  #f38ba8
color2  #a6e3a1
color3  #f9e2af
color4  #89b4fa
color5  #f5c2e7
color6  #94e2d5
color7  #bac2de
```


**3️⃣ Установка fastfetch:**

```
sudo pacman -S fastfetch
```


**4️⃣ Настройка конфигураций fastfetch**

**Открой:**

```
sudo micro /home/user/.config/fastfetch/config.jsonc
```

**Замены все на вот это:**

```
{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json",
    "logo": {
        "type": "builtin",
        "height": 15,
        "width": 30,
        "padding": {
            "top": 5,
            "left": 3
        }
    },
    "modules": [
        "break",
        {
            "type": "custom",
            "format": "\u001b[90m┌──────────────────────Hardware──────────────────────┐"
        },
        {
            "type": "host",
            "key": " PC",
            "keyColor": "green"
        },
        {
            "type": "cpu",
            "key": "│ ├",
            "keyColor": "green"
        },
        {
            "type": "gpu",
            "key": "│ ├󰍛",
            "keyColor": "green"
        },
        {
            "type": "memory",
            "key": "│ ├󰍛",
            "keyColor": "green"
        },
        {
            "type": "disk",
            "key": "└ └",
            "keyColor": "green"
        },
        {
            "type": "custom",
            "format": "\u001b[90m└────────────────────────────────────────────────────┘"
        },
        "break",
        {
            "type": "custom",
            "format": "\u001b[90m┌──────────────────────Software──────────────────────┐"
        },
        {
            "type": "os",
            "key": " OS",
            "keyColor": "yellow"
        },
        {
            "type": "kernel",
            "key": "│ ├",
            "keyColor": "yellow"
        },
        {
            "type": "bios",
            "key": "│ ├",
            "keyColor": "yellow"
        },
        {
            "type": "packages",
            "key": "│ ├󰏖",
            "keyColor": "yellow"
        },
        {
            "type": "shell",
            "key": "└ └",
            "keyColor": "yellow"
        },
        "break",
        {
            "type": "de",
            "key": " DE",
            "keyColor": "blue"
        },
        {
            "type": "lm",
            "key": "│ ├",
            "keyColor": "blue"
        },
        {
            "type": "wm",
            "key": "│ ├",
            "keyColor": "blue"
        },
        {
            "type": "wmtheme",
            "key": "│ ├󰉼",
            "keyColor": "blue"
        },
        {
            "type": "terminal",
            "key": "└ └",
            "keyColor": "blue"
        },
        {
            "type": "custom",
            "format": "\u001b[90m└────────────────────────────────────────────────────┘"
        },
        "break",
        {
            "type": "custom",
            "format": "\u001b[90m┌────────────────────Uptime / Age / DT────────────────────┐"
        },
        {
            "type": "command",
            "key": "  OS Age ",
            "keyColor": "magenta",
            "text": "birth_install=$(stat -c %W /); current=$(date +%s); time_progression=$((current - birth_install)); days_difference=$((time_progression / 86400)); echo $days_difference days"
        },
        {
            "type": "uptime",
            "key": "  Uptime ",
            "keyColor": "magenta"
        },
        {
            "type": "datetime",
            "key": "  DateTime ",
            "keyColor": "magenta"
        },
        {
            "type": "custom",
            "format": "\u001b[90m└─────────────────────────────────────────────────────────┘"
        },

//        {
//            "type": "colors"
//        },

        {
            "type": "colors",
            "paddingLeft": 2,
            "symbol": "circle"
        }
 
    ]
}
```


**5️⃣ Настройка fish**

```
cash -s $(which fish)
```


---

**----------------------------------------------------------**