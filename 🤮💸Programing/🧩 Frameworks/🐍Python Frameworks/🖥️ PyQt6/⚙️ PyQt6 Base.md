Связано с [[🖥️ PyQt6]]

<div align="center">  
<h2>⚙️ PyQt6 Base — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>PyQt6 Base</h3> 
</div>
---
`PyQt6` — это Python-обёртка над фреймворком **Qt6**.

**Позволяет создавать:**

- desktop приложения
    
- GUI утилиты
    
- сложные интерфейсы
    
- инструменты разработчика

**Работает на:**

- Linux
    
- Windows
    
- macOS

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install PyQt6
```

**Дополнительно часто устанавливают:**

```bash
pip install PyQt6-tools
```

**`PyQt6-tools` содержит:**

- Qt Designer
    
- дополнительные инструменты

---

<div align="center">  
<h3>Основные модули PyQt6</h3> 
</div>
---

| **модуль**  |       **описание**       |
| :---------: | :----------------------: |
| `QtWidgets` | интерфейс (кнопки, окна) |
|  `QtCore`   |     базовые функции      |
|   `QtGui`   |         графика          |
| `QtNetwork` |           сеть           |
|   `QtSql`   |       работа с БД        |

---

<div align="center">  
<h3>Минимальное приложение</h3> 
</div>
---
```python
import sys
from PyQt6.QtWidgets import QApplication, QWidget

app = QApplication(sys.argv)

window = QWidget()
window.show()

app.exec()
```

---

<div align="center">  
<h3>QApplication</h3> 
</div>
---
```python
app = QApplication(sys.argv)
```

Это **основной объект приложения**.

**Он:**

- управляет событиями
    
- запускает GUI

---

<div align="center">  
<h3>Главное окно</h3> 
</div>
---
```python
window = QWidget()
```

*`QWidget` — базовый элемент интерфейса.*

**Показать окно:**

```python
window.show()
```

---

<div align="center">  
<h3>Завершение программы</h3> 
</div>
---
```python
app.exec()
```

*Запускает **event loop**.*

---

<div align="center">  
<h3>QMainWindow</h3> 
</div>
---
**Для полноценных приложений используется:**

```python
from PyQt6.QtWidgets import QMainWindow
```

**Пример:**

```python
class MainWindow(QMainWindow):

    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")
```

---

<div align="center">  
<h3>Размер окна</h3> 
</div>
---
```python
window.resize(800, 600)
```

**или:**

```python
window.setFixedSize(800,600)
```

---

<div align="center">  
<h3>Заголовок окна</h3> 
</div>
---
```python
window.setWindowTitle("My Application")
```

---

<div align="center">  
<h3>Иконка окна</h3> 
</div>
---
```python
from PyQt6.QtGui import QIcon

window.setWindowIcon(QIcon("icon.png"))
```

---

<div align="center">  
<h3>Layout (расположение элементов</h3> 
</div>
---
Layouts управляют **расположением виджетов**.

**Основные:**

|  **Layout**   |  **описание**  |
| :-----------: | :------------: |
| `QVBoxLayout` |  вертикальный  |
| `QHBoxLayout` | горизонтальный |
| `QGridLayout` |     сетка      |

---

<div align="center">  
<h3>Вертикальный Layout</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QVBoxLayout

layout = QVBoxLayout()
```

**Добавление виджета:**

```python
layout.addWidget(button)
```

---

<div align="center">  
<h3>Горизонтальный Layout</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QHBoxLayout

layout = QHBoxLayout()
```

---

<div align="center">  
<h3>Grid Layout</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QGridLayout

layout = QGridLayout()

layout.addWidget(button, 0, 0)
```

---

<div align="center">  
<h3>Основные виджеты</h3> 
</div>
---

|  **виджет**   |   **описание**    |
| :-----------: | :---------------: |
| `QPushButton` |      кнопка       |
|   `QLabel`    |       текст       |
|  `QLineEdit`  |    поле ввода     |
|  `QTextEdit`  |  текстовое поле   |
|  `QCheckBox`  |      чекбокс      |
|  `QComboBox`  | выпадающий список |

---

<div align="center">  
<h3>Кнопка</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QPushButton

button = QPushButton("Click me")
```

---

<div align="center">  
<h3>Текст</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QLabel

label = QLabel("Hello")
```

---

<div align="center">  
<h3>Поле ввода</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QLineEdit

input = QLineEdit()
```

**Получить текст:**

```python
text = input.text()
```

---

<div align="center">  
<h3>QTextEdit</h3> 
</div>
---
**Многострочное поле:**

```python
from PyQt6.QtWidgets import QTextEdit

text = QTextEdit()
```

---

<div align="center">  
<h3>Signals и Slots</h3> 
</div>
---
PyQt работает через **сигналы и слоты**.

**Пример:**

```python
button.clicked.connect(function)
```

---

<div align="center">  
<h3>Пример кнопки</h3> 
</div>
---
```python
def click():
    print("Button clicked")

button.clicked.connect(click)
```

---

<div align="center">  
<h3>Lambda</h3> 
</div>
---
**Можно использовать lambda:**

```python
button.clicked.connect(lambda: print("Click"))
```

---

<div align="center">  
<h3>Пример полного приложения</h3> 
</div>
---
```python
import sys
from PyQt6.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton

app = QApplication(sys.argv)

window = QWidget()

layout = QVBoxLayout()

button = QPushButton("Click me")

layout.addWidget(button)

window.setLayout(layout)

window.show()

app.exec()
```

---

<div align="center">  
<h3>Диалоговые окна</h3> 
</div>
---
**Сообщение:**

```python
from PyQt6.QtWidgets import QMessageBox
```

**Пример:**

```python
QMessageBox.information(window, "Title", "Hello")
```

---

<div align="center">  
<h3>QFileDialog</h3> 
</div>
---
**Выбор файла:**

```python
from PyQt6.QtWidgets import QFileDialog

file = QFileDialog.getOpenFileName()
```

---

<div align="center">  
<h3>Таймер</h3> 
</div>
---
```python
from PyQt6.QtCore import QTimer
```

**Пример:**

```python
timer = QTimer()

timer.timeout.connect(function)

timer.start(1000)
```

*`1000` = 1 секунда.*

---

<div align="center">  
<h3>Работа с изображениями</h3> 
</div>
---
```python
from PyQt6.QtGui import QPixmap

pixmap = QPixmap("image.png")

label.setPixmap(pixmap)
```

---

<div align="center">  
<h3>Styles (CSS)</h3> 
</div>
---
Qt поддерживает **CSS стили**.

```python
button.setStyleSheet("""
background-color: red;
color: white;
""")
```

---

<div align="center">  
<h3>Qt Designer</h3> 
</div>
---
`Qt Designer` — визуальный редактор интерфейсов.

**Файл:**

```
.ui
```

**Можно:**

- рисовать интерфейс
    
- генерировать Python код

---

<div align="center">  
<h3>Конвертация .ui</h3> 
</div>
---
```bash
pyuic6 design.ui -o ui.py
```

---

<div align="center">  
<h3>Структура проекта</h3> 
</div>
---
**Типичный проект:**

```
project/

main.py
ui/
resources/
```

---

<div align="center">  
<h3>Когда использовать PyQt6</h3> 
</div>
---
**Подходит для:**

- desktop приложений
    
- GUI инструментов
    
- админ панелей
    
- IDE / утилит

---

<div align="center">  
<h3>Ограничения</h3> 
</div>
---
**PyQt6:**

- большой размер приложения
    
- сложнее чем Tkinter

---

<div align="center">  
<h3>Альтернативы</h3> 
</div>
---

| **библиотека** |      **описание**      |
| :------------: | :--------------------: |
|    Tkinter     |    стандартный GUI     |
|    PySide6     | официальный Qt binding |
|      Kivy      |     мобильные GUI      |

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
`PyQt6` — мощная библиотека для **GUI приложений Python**.

**Основные шаги:**

1. создать `QApplication`
    
2. создать окно
    
3. добавить виджеты
    
4. подключить сигналы
    
5. запустить `app.exec()`

---

**--------------------------------------------------------**