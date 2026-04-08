Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>📦 PyInstaller — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>PyInstaller</h3> 
</div>
---
`PyInstaller` — библиотека для упаковки Python-скриптов в исполняемые файлы.

**Поддерживаемые платформы:**

- Linux
    
- Windows
    
- macOS

*После сборки создаётся самостоятельная программа, которая не требует установленного Python.*

---

<div align="center">  
<h4>Установка</h4> 
</div>
---
```bash
pip install pyinstaller
```

**Проверка:**

```bash
pyinstaller --version
```

---

<div align="center">  
<h4>Базовый синтаксис</h4> 
</div>
---
```bash
pyinstaller [OPTIONS] script.py
```

**Пример:**

```bash
pyinstaller main.py
```

---

<div align="center">  
<h4>Результат сборки</h4> 
</div>
---
**После сборки появляются:**

```
build/
dist/
script.spec
```

| **Элемент** |   **Назначение**    |
| :---------: | :-----------------: |
|   `build`   |   временные файлы   |
|   `dist`    |  готовая программа  |
|   `.spec`   | конфигурация сборки |

---

<div align="center">  
<h4>Основные режимы сборки</h4> 
</div>
---
**OneDir (по умолчанию):**

```bash
pyinstaller main.py
```

**Результат:**

```
dist/
 └ main/
     main.exe
     библиотеки
     python runtime
```

**Особенности:**

- быстрее запуск
    
- легче дебажить
    
- много файлов

---
**OneFile:**

```bash
pyinstaller --onefile main.py
```

**Результат:**

```
dist/main.exe
```

**Особенности:**

- один файл
    
- при запуске распаковывается во временную папку

---

<div align="center">  
<h3>Основные аргументы PyInstaller</h3> 
</div>
---

<div align="center">  
<h4>--onefile</h4> 
</div>
===========================
*Создаёт один исполняемый файл.*

```bash
pyinstaller --onefile main.py
```

===========================

<div align="center">  
<h4>--onedir</h4> 
</div>
===========================
*Сборка в папку (режим по умолчанию).*

```bash
pyinstaller --onedir main.py
```

===========================

<div align="center">  
<h4>--windowed</h4> 
</div>
===========================
*Отключает консоль.*

Используется для **GUI приложений**.

```bash
pyinstaller --windowed app.py
```

**Аналог:**

```
-w
```

===========================

<div align="center">  
<h4>--console</h4> 
</div>
===========================
*Принудительно включает консоль.*

```bash
pyinstaller --console main.py
```

===========================

<div align="center">  
<h4>--name</h4> 
</div>
===========================
*Имя программы.*

```bash
pyinstaller --name MyApp main.py
```

**Результат:**

```
dist/MyApp.exe
```

===========================

<div align="center">  
<h4>--icon</h4> 
</div>
===========================
*Добавляет иконку.*

```bash
pyinstaller --icon icon.ico main.py
```

===========================

<div align="center">  
<h4>--add-data</h4> 
</div>
===========================
*Добавляет дополнительные файлы.*

**Используется для:**

- изображений
    
- конфигов
    
- базы данных
    
- ресурсов

**Linux / macOS:**

```bash
--add-data "file.txt:."
```

**Windows:**

```bash
--add-data "file.txt;."
```

**Пример:**

```bash
pyinstaller --add-data "config.json;." main.py
```

===========================

<div align="center">  
<h4>--add-binary</h4> 
</div>
===========================
*Добавляет бинарные файлы.*

```bash
pyinstaller --add-binary "lib.dll;."
```

===========================

<div align="center">  
<h4>--hidden-import</h4> 
</div>
===========================
*Добавляет модуль, который PyInstaller не обнаружил.*

```bash
pyinstaller --hidden-import module main.py
```

**Пример:**

```bash
pyinstaller --hidden-import telebot bot.py
```

===========================

<div align="center">  
<h4>--collect-all</h4> 
</div>
===========================
*Добавляет все данные библиотеки.*

```bash
pyinstaller --collect-all package_name main.py
```

===========================

<div align="center">  
<h4>--collect-data</h4> 
</div>
===========================
*Добавляет data файлы библиотеки.*

```bash
pyinstaller --collect-data package main.py
```

===========================

<div align="center">  
<h4>--paths</h4> 
</div>
===========================
*Добавляет путь для поиска модулей.*

```bash
pyinstaller --paths src main.py
```

===========================

<div align="center">  
<h4>--clean</h4> 
</div>
===========================
*Удаляет кеш сборки.*

```bash
pyinstaller --clean main.py
```

===========================

<div align="center">  
<h4>--log-level</h4> 
</div>
===========================
*Уровень логирования.*

```bash
pyinstaller --log-level DEBUG main.py
```

**Уровни:**

```
TRACE
DEBUG
INFO
WARN
ERROR
```

===========================

<div align="center">  
<h4>--distpath</h4> 
</div>
===========================
*Папка для готовой программы.*

```bash
pyinstaller --distpath output main.py
```

===========================

<div align="center">  
<h4>--workpath</h4> 
</div>
===========================
*Папка для build файлов.*

```bash
pyinstaller --workpath build_temp main.py
```

===========================

<div align="center">  
<h4>--specpath</h4> 
</div>
===========================
*Папка для `.spec` файла.*

```bash
pyinstaller --specpath build_spec main.py
```

===========================

<div align="center">  
<h4>--runtime-tmpdir</h4> 
</div>
===========================
*Папка временной распаковки (для onefile).*

```bash
pyinstaller --runtime-tmpdir temp main.py
```

===========================

<div align="center">  
<h4>--upx-dir</h4> 
</div>
===========================
*Путь к UPX компрессору.*

```bash
pyinstaller --upx-dir /usr/bin main.py
```

===========================

<div align="center">  
<h4>--strip</h4> 
</div>
===========================
*Удаляет debug символы.*

*Уменьшает размер файла.*

```bash
pyinstaller --strip main.py
```

===========================

<div align="center">  
<h4>--noupx</h4> 
</div>
===========================
*Отключает UPX.*

```bash
pyinstaller --noupx main.py
```

===========================

<div align="center">  
<h4>--version-file</h4> 
</div>
===========================
*Добавляет информацию о версии (Windows).*

```bash
pyinstaller --version-file version.txt main.py
```

===========================

---

<div align="center">  
<h3>Spec файл</h3> 
</div>
---
**После первой сборки создаётся:**

```
script.spec
```

**Использование:**

```bash
pyinstaller script.spec
```

**Spec позволяет:**

- менять зависимости
    
- добавлять файлы
    
- настраивать сборку

---

<div align="center">  
<h3>Работа с ресурсами</h3> 
</div>
---
*При использовании `--onefile` путь к файлам меняется.*

**Пример функции:**

```python
import sys
import os

def resource_path(path):
    try:
        base = sys._MEIPASS
    except Exception:
        base = os.path.abspath(".")

    return os.path.join(base, path)
```

**Использование:**

```python
config = resource_path("config.json")
```

---

<div align="center">  
<h3>Примеры</h3> 
</div>
---
**Минимальная сборка:**

```bash
pyinstaller main.py
```

---
**Один exe:**

```bash
pyinstaller --onefile main.py
```

---
**GUI приложение:**

```bash
pyinstaller --onefile --windowed app.py
```

---
**С иконкой:**

```bash
pyinstaller --onefile --icon icon.ico main.py
```

---
**С ресурсами:**

```bash
pyinstaller \
--onefile \
--add-data "config.json;." \
--add-data "images;images" \
main.py
```

---

<div align="center">  
<h3>Типичные проблемы</h3> 
</div>
---
**ModuleNotFoundError:**

**Решение:**

```
--hidden-import
```

---
**Файлы не находятся:**

**Использовать:**

```
resource_path()
```

---

<div align="center">  
<h3>Большой размер exe</h3> 
</div>
---
**Причина:**

- Python runtime
    
- библиотеки

**Размер обычно:**

```
10–70 MB
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
*PyInstaller используется для:*

- создания `.exe`
    
- распространения Python программ
    
- упаковки зависимостей

**Основные режимы:**

```
onefile
onedir
```

**Основные аргументы:**

```
--onefile
--windowed
--icon
--add-data
--hidden-import
--clean
```

---

**--------------------------------------------------------**