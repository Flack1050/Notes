Связано с [[🖥️ PyQt6]]

<div align="center">  
<h2>📐 PyQt6 Layout System — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>PyQt6 Layout System</h3> 
</div>
---
**Layout** — это система, которая **управляет расположением Widgets в окне**.

**Она автоматически:**

- располагает элементы
    
- изменяет их размер
    
- адаптирует интерфейс при изменении окна

*Без Layout интерфейс делают через `move()` и `resize()`, но это **плохая практика**.*

---

<div align="center">  
<h3>Основные Layout</h3> 
</div>
---

|  **Layout**   | **описание**  |
| :-----------: | :-----------: |
| `QVBoxLayout` |  вертикально  |
| `QHBoxLayout` | горизонтально |
| `QGridLayout` |     сетка     |
| `QFormLayout` |     формы     |

---

<div align="center">  
<h3>QVBoxLayout</h3> 
</div>
---
Размещает элементы **вертикально сверху вниз**.

```python
from PyQt6.QtWidgets import QVBoxLayout
```

**Пример:**

```python
layout = QVBoxLayout()

layout.addWidget(button1)
layout.addWidget(button2)
layout.addWidget(button3)
```

**Результат:**

```
Button1
Button2
Button3
```

---

<div align="center">  
<h3>QHBoxLayout</h3> 
</div>
---
Размещает элементы **слева направо**.

```python
from PyQt6.QtWidgets import QHBoxLayout
```

**Пример:**

```python
layout = QHBoxLayout()

layout.addWidget(button1)
layout.addWidget(button2)
layout.addWidget(button3)
```

**Результат:**

```
Button1 Button2 Button3
```

---

<div align="center">  
<h3>QGridLayout</h3> 
</div>
---
Размещает элементы **по сетке (строки и колонки)**.

```python
from PyQt6.QtWidgets import QGridLayout
```

**Пример:**

```python
layout = QGridLayout()

layout.addWidget(button1, 0, 0)
layout.addWidget(button2, 0, 1)
layout.addWidget(button3, 1, 0)
```

**Параметры:**

```
(row, column)
```

**Пример сетки:**

```
Button1 Button2
Button3
```

---

<div align="center">  
<h3>QFormLayout</h3> 
</div>
---
Используется для **форм ввода данных**.

```python
from PyQt6.QtWidgets import QFormLayout
```

**Пример:**

```python
layout = QFormLayout()

layout.addRow("Name:", name_input)
layout.addRow("Email:", email_input)
```

**Результат:**

```
Name:  [______]
Email: [______]
```

---

<div align="center">  
<h3>Установка Layout</h3> 
</div>
---
Layout нужно установить для окна.

**Пример:**

```python
window.setLayout(layout)
```

---

<div align="center">  
<h3>Добавление Widget</h3> 
</div>
---
**Основной метод:**

```python
layout.addWidget(widget)
```

**Для вложенных layout:**

```python
layout.addLayout(other_layout)
```

---

<div align="center">  
<h3>Отступы</h3> 
</div>
---
```python
layout.setSpacing(10)
```

**Отступы от края окна:**

```python
layout.setContentsMargins(10,10,10,10)
```

---

<div align="center">  
<h3>Stretch (растягивание)</h3> 
</div>
---
Позволяет управлять **как элементы занимают пространство**.

```python
layout.addWidget(widget1)
layout.addWidget(widget2)

layout.setStretch(0,1)
layout.setStretch(1,2)
```

*Второй элемент будет **в 2 раза больше**.*

---

<div align="center">  
<h3>Вложенные Layout</h3> 
</div>
---
Layouts можно **комбинировать**.

**Пример:**

```python
main_layout = QVBoxLayout()

top_layout = QHBoxLayout()
bottom_layout = QHBoxLayout()

top_layout.addWidget(button1)
top_layout.addWidget(button2)

bottom_layout.addWidget(button3)

main_layout.addLayout(top_layout)
main_layout.addLayout(bottom_layout)
```

**Структура:**

```
Button1 Button2
Button3
```

---

<div align="center">  
<h3>Минимальный пример</h3> 
</div>
---
```python
import sys
from PyQt6.QtWidgets import QApplication, QWidget, QPushButton, QVBoxLayout

app = QApplication(sys.argv)

window = QWidget()

layout = QVBoxLayout()

layout.addWidget(QPushButton("Button 1"))
layout.addWidget(QPushButton("Button 2"))

window.setLayout(layout)

window.show()

app.exec()
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
**Основные Layout:**

```
QVBoxLayout
QHBoxLayout
QGridLayout
QFormLayout
```

**Основные методы:**

```
addWidget()
addLayout()
setSpacing()
setContentsMargins()
setStretch()
```

---

**--------------------------------------------------------**