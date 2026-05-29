Связано с [[🖥️ PyQt6]]

<div align="center">  
<h2>📡 PyQt6 Signals & Slots — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>PyQt6 Signals & Slots</h3> 
</div>
---
**Signals & Slots** — это система обработки **событий в PyQt**.

**Когда происходит событие:**

```
Signal → Slot
```

- **Signal** — сигнал (событие)
    
- **Slot** — функция, которая выполняется

---

<div align="center">  
<h3>Как это работает</h3> 
</div>
---
**Пример:**

```
Button Click → функция выполняется
```

**Пример кода:**

```python
button.clicked.connect(my_function)
```

**Расшифровка:**

|  **элемент**  | **значение** |
| :-----------: | :----------: |
|   `button`    |    виджет    |
|   `clicked`   |    сигнал    |
|  `connect()`  |  соединяет   |
| `my_function` |   функция    |

---

<div align="center">  
<h3>Пример</h3> 
</div>
---
```python
import sys
from PyQt6.QtWidgets import QApplication, QWidget, QPushButton

app = QApplication(sys.argv)

window = QWidget()

button = QPushButton("Click me", window)

def hello():
    print("Hello")

button.clicked.connect(hello)

window.show()
app.exec()
```

*Когда нажимаешь кнопку → вызывается `hello()`.*

---

<div align="center">  
<h3>Основные сигналы</h3> 
</div>
---

<div align="center">  
<h4>QPushButton</h4> 
</div>
===========================

| **сигнал** |          **описание**          |
| :--------: | :----------------------------: |
| `clicked`  |         кнопка нажата          |
| `pressed`  | кнопка нажата (момент нажатия) |
| `released` |        кнопка отпущена         |

===========================

<div align="center">  
<h4>QLineEdit</h4> 
</div>
===========================

|   **сигнал**    |  **описание**   |
| :-------------: | :-------------: |
|  `textChanged`  | текст изменился |
| `returnPressed` |  нажали Enter   |

===========================

<div align="center">  
<h4>QCheckBox</h4> 
</div>
===========================

|   **сигнал**   |     **описание**     |
| :------------: | :------------------: |
| `stateChanged` | состояние изменилось |

===========================

<div align="center">  
<h4>QComboBox</h4> 
</div>
===========================

|      **сигнал**       | **описание**  |
| :-------------------: | :-----------: |
| `currentIndexChanged` | изменён выбор |

===========================

---

<div align="center">  
<h3>Передача аргументов</h3> 
</div>
---
Иногда сигнал передаёт данные.

**Пример:**

```python
def text_changed(text):
    print(text)

input.textChanged.connect(text_changed)
```

---

<div align="center">  
<h3>Lambda</h3> 
</div>
---
Иногда нужно передать **свой аргумент**.

**Пример:**

```python
button.clicked.connect(lambda: hello("World"))
```

**Функция:**

```python
def hello(name):
    print(name)
```

---

<div align="center">  
<h3>Отключение сигнала</h3> 
</div>
---
Можно отключить сигнал.

```python
button.clicked.disconnect()
```

---

<div align="center">  
<h3>Несколько слотов</h3> 
</div>
---
К одному сигналу можно подключить **несколько функций**.

```python
button.clicked.connect(func1)
button.clicked.connect(func2)
```

*Обе функции будут вызваны.*

---

<div align="center">  
<h3>Пользовательские сигналы</h3> 
</div>
---
Можно создавать **свои сигналы**.

```python
from PyQt6.QtCore import pyqtSignal
```

**Пример:**

```python
class MyWidget(QWidget):

    my_signal = pyqtSignal()

    def send_signal(self):
        self.my_signal.emit()
```

**Подключение:**

```python
widget.my_signal.connect(function)
```

---

<div align="center">  
<h3>emit()</h3> 
</div>
---
`emit()` — отправляет сигнал.

```python
self.my_signal.emit()
```

---

<div align="center">  
<h3>Signal с аргументами</h3> 
</div>
---
```python
from PyQt6.QtCore import pyqtSignal

class MyWidget(QWidget):

    my_signal = pyqtSignal(str)

    def send(self):
        self.my_signal.emit("Hello")
```

---

<div align="center">  
<h3>Блокировка сигналов</h3> 
</div>
---
Иногда нужно **временно отключить сигналы**.

```python
widget.blockSignals(True)
```

**Включить обратно:**

```python
widget.blockSignals(False)
```

---

<div align="center">  
<h3>Полный пример</h3> 
</div>
---
```python
import sys
from PyQt6.QtWidgets import QApplication, QWidget, QPushButton, QVBoxLayout

app = QApplication(sys.argv)

window = QWidget()

layout = QVBoxLayout()

button = QPushButton("Click")

def clicked():
    print("Button pressed")

button.clicked.connect(clicked)

layout.addWidget(button)

window.setLayout(layout)

window.show()

app.exec()
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
**Система событий в PyQt:**

```
Signal → Slot
```

**Основные методы:**

```
connect()
disconnect()
emit()
```

**Самые используемые сигналы:**

```
clicked
textChanged
stateChanged
currentIndexChanged
```

---

**--------------------------------------------------------**