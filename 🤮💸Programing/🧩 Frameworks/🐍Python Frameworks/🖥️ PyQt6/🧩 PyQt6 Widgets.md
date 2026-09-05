Связано с [[🖥️ PyQt6]]

<div align="center">  
<h2>🧩 PyQt6 Widgets — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>PyQt6 Widgets</h3> 
</div>
---
**Widgets** — это элементы интерфейса GUI.

**Примеры:**

- кнопки
    
- текст
    
- поля ввода
    
- списки
    
- таблицы

**Все виджеты наследуются от:**

```
QWidget
```

---

<div align="center">  
<h3>Базовый Widget</h3> 
</div>
---
```python
from PyQt6.QtWidgets import QWidget
```

**Пример:**

```python
window = QWidget()
window.show()
```

**Основные методы:**

|     **метод**      |  **описание**   |
| :----------------: | :-------------: |
|      `show()`      |  показать окно  |
|      `hide()`      |     скрыть      |
|     `resize()`     | изменить размер |
|      `move()`      |   переместить   |
| `setWindowTitle()` |    заголовок    |

---

<div align="center">  
<h3>QLabel</h3> 
</div>
---
Отображает **текст или изображение**.

```python
from PyQt6.QtWidgets import QLabel

label = QLabel("Hello")
```

**Основные методы:**

|   **метод**   |      **описание**      |
| :-----------: | :--------------------: |
|  `setText()`  |     изменить текст     |
|   `text()`    |     получить текст     |
| `setPixmap()` | установить изображение |

**Пример:**

```python
label.setText("New text")
```

---

<div align="center">  
<h3>QPushButton</h3> 
</div>
---
Кнопка.

```python
from PyQt6.QtWidgets import QPushButton

button = QPushButton("Click")
```

**Сигналы:**

```
clicked
pressed
released
```

**Пример:**

```python
button.clicked.connect(function)
```

**Методы:**

|   **метод**    |     **описание**     |
| :------------: | :------------------: |
|  `setText()`   |     текст кнопки     |
| `setEnabled()` | включить / выключить |

---

<div align="center">  
<h3>QLineEdit</h3> 
</div>
---
Однострочное поле ввода.

```python
from PyQt6.QtWidgets import QLineEdit

input = QLineEdit()
```

**Методы:**

|  **метод**  |   **описание**   |
| :---------: | :--------------: |
|  `text()`   |  получить текст  |
| `setText()` | установить текст |
|  `clear()`  |     очистить     |

**Пример:**

```python
text = input.text()
```

---

<div align="center">  
<h3>QTextEdit</h3> 
</div>
---
Многострочное поле ввода.

```python
from PyQt6.QtWidgets import QTextEdit

text = QTextEdit()
```

**Методы:**

|    **метод**     |   **описание**   |
| :--------------: | :--------------: |
| `toPlainText()`  |  получить текст  |
| `setPlainText()` | установить текст |
|    `clear()`     |     очистить     |

---

<div align="center">  
<h3>QCheckBox</h3> 
</div>
---
Чекбокс.

```python
from PyQt6.QtWidgets import QCheckBox

check = QCheckBox("Enable")
```

**Методы:**

|   **метод**    | **описание** |
| :------------: | :----------: |
| `isChecked()`  |  состояние   |
| `setChecked()` |   изменить   |

**Пример:**

```python
if check.isChecked():
    print("Enabled")
```

---

<div align="center">  
<h3>QRadioButton</h3> 
</div>
---
Кнопка выбора (один из вариантов).

```python
from PyQt6.QtWidgets import QRadioButton

radio = QRadioButton("Option")
```

**Методы:**

|   **метод**   | **описание** |
| :-----------: | :----------: |
| `isChecked()` |  выбран ли   |

---

<div align="center">  
<h3>QComboBox</h3> 
</div>
---
Выпадающий список.

```python
from PyQt6.QtWidgets import QComboBox

combo = QComboBox()
```

**Добавление элементов:**

```python
combo.addItem("Item 1")
combo.addItem("Item 2")
```

**Методы:**

|    **метод**    |   **описание**   |
| :-------------: | :--------------: |
| `currentText()` | выбранный текст  |
|   `addItem()`   | добавить элемент |
|    `clear()`    |     очистить     |

---

<div align="center">  
<h3>QListWidget</h3> 
</div>
---
Список элементов.

```python
from PyQt6.QtWidgets import QListWidget

list_widget = QListWidget()
```

**Добавление элемента:**

```python
list_widget.addItem("Item")
```

**Методы:**

|    **метод**    | **описание** |
| :-------------: | :----------: |
|   `addItem()`   |   добавить   |
| `currentItem()` |  выбранный   |

---

<div align="center">  
<h3>QTableWidget</h3> 
</div>
---
Таблица.

```python
from PyQt6.QtWidgets import QTableWidget

table = QTableWidget()
```

**Размер таблицы:**

```python
table.setRowCount(5)
table.setColumnCount(3)
```

**Установка значения:**

```python
from PyQt6.QtWidgets import QTableWidgetItem

table.setItem(0,0,QTableWidgetItem("Hello"))
```

---

<div align="center">  
<h3>QSlider</h3> 
</div>
---
Ползунок.

```python
from PyQt6.QtWidgets import QSlider
from PyQt6.QtCore import Qt

slider = QSlider(Qt.Orientation.Horizontal)
```

**Методы:**

|  **метод**   | **описание** |
| :----------: | :----------: |
|  `value()`   |   значение   |
| `setValue()` |   изменить   |

---

<div align="center">  
<h3>QProgressBar</h3> 
</div>
---
Индикатор прогресса.

```python
from PyQt6.QtWidgets import QProgressBar

progress = QProgressBar()
```

**Установка значения:**

```python
progress.setValue(50)
```

---

<div align="center">  
<h3>QSpinBox</h3> 
</div>
---
Поле для чисел.

```python
from PyQt6.QtWidgets import QSpinBox

spin = QSpinBox()
```

**Методы:**

|  **метод**   |  **описание**  |
| :----------: | :------------: |
|  `value()`   | получить число |
| `setValue()` |   установить   |

---

<div align="center">  
<h3>QTabWidget</h3> 
</div>
---
Вкладки.

```python
from PyQt6.QtWidgets import QTabWidget
```

**Добавление вкладки:**

```python
tabs.addTab(widget, "Tab name")
```

---

<div align="center">  
<h3>QGroupBox</h3> 
</div>
---
Группа элементов.

```python
from PyQt6.QtWidgets import QGroupBox

group = QGroupBox("Settings")
```

---

<div align="center">  
<h3>QFrame</h3> 
</div>
---
Контейнер / разделитель.

```python
from PyQt6.QtWidgets import QFrame

frame = QFrame()
```

---

<div align="center">  
<h3>QScrollArea</h3> 
</div>
---
Область прокрутки.

```python
from PyQt6.QtWidgets import QScrollArea

scroll = QScrollArea()
```

---

<div align="center">  
<h3>QFileDialog</h3> 
</div>
---
Выбор файла.

```python
from PyQt6.QtWidgets import QFileDialog
```

**Пример:**

```python
file = QFileDialog.getOpenFileName()
```

---

<div align="center">  
<h3>QColorDialog</h3> 
</div>
---
Выбор цвета.

```python
from PyQt6.QtWidgets import QColorDialog

color = QColorDialog.getColor()
```

---

<div align="center">  
<h3>QFontDialog</h3> 
</div>
---
Выбор шрифта.

```python
from PyQt6.QtWidgets import QFontDialog

font = QFontDialog.getFont()
```

---

<div align="center">  
<h3>QMessageBox</h3> 
</div>
---
Сообщения.

```python
from PyQt6.QtWidgets import QMessageBox
```

**Пример:**

```python
QMessageBox.information(window,"Info","Hello")
```

**Типы:**

|    **тип**    |  **описание**  |
| :-----------: | :------------: |
| `information` |   информация   |
|   `warning`   | предупреждение |
|  `critical`   |     ошибка     |

---

<div align="center">  
<h3>Контейнеры Widgets</h3> 
</div>
---
Контейнеры позволяют **группировать элементы**.

|  **Widget**   | **описание** |
| :-----------: | :----------: |
|   `QWidget`   |   базовый    |
|   `QFrame`    |  контейнер   |
|  `QGroupBox`  |    группа    |
| `QTabWidget`  |   вкладки    |
| `QScrollArea` |  прокрутка   |

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Widgets — основные элементы GUI в PyQt6.

**Самые используемые:**

```
QPushButton
QLabel
QLineEdit
QTextEdit
QComboBox
QCheckBox
QTableWidget
```

**Контейнеры:**

```
QWidget
QFrame
QTabWidget
QGroupBox
```

---

**--------------------------------------------------------**