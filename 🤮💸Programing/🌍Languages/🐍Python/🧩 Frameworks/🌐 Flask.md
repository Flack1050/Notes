Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🌐 Flask — Documentation</h2> 
</div>
**--------------------------------------------------------**


<div align="center">  
<h3>Flask</h3> 
</div>
---
`Flask` — это **микрофреймворк для создания веб-приложений на Python**.

**Позволяет создавать:**

- сайты
    
- REST API
    
- backend для приложений
    
- веб-панели

**Особенности:**

- минималистичный
    
- легко расширяется
    
- очень популярен для API

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install flask
```

**Проверка:**

```python
import flask
print(flask.__version__)
```

---

<div align="center">  
<h3>Минимальное приложение</h3> 
</div>
---
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello World"

app.run()
```

**После запуска сервер работает на:**

```
http://127.0.0.1:5000
```

---

<div align="center">  
<h3>Основной объект приложения</h3> 
</div>
---
```python
app = Flask(__name__)
```

`Flask` создаёт **основной объект приложения**.

---

<div align="center">  
<h3>Запуск сервера</h3> 
</div>
---
```python
app.run()
```

**Параметры:**

```python
app.run(
    host="0.0.0.0",
    port=5000,
    debug=True
)
```

| **параметр** |   **описание**   |
| :----------: | :--------------: |
|     host     |  адрес сервера   |
|     port     |       порт       |
|    debug     | режим разработки |

---

<div align="center">  
<h3>Routes (маршруты)</h3> 
</div>
---
Route определяет **URL и функцию обработчик**.

```python
@app.route("/")
def home():
    return "Home page"
```

---

<div align="center">  
<h3>Несколько страниц</h3> 
</div>
---
```python
@app.route("/")
def home():
    return "Home"

@app.route("/about")
def about():
    return "About page"
```

---

<div align="center">  
<h3>Методы HTTP</h3> 
</div>
---
Flask поддерживает HTTP методы.

```python
@app.route("/login", methods=["GET", "POST"])
def login():
    return "Login"
```

**Основные методы:**

| **метод** |  **назначение**  |
| :-------: | :--------------: |
|    GET    | получение данных |
|   POST    | отправка данных  |
|    PUT    |    обновление    |
|  DELETE   |     удаление     |

---

<div align="center">  
<h3>Параметры в URL</h3> 
</div>
---
```python
@app.route("/user/<name>")
def user(name):
    return f"Hello {name}"
```

**URL:**

```
/user/Alice
```

---

<div align="center">  
<h3>Типы параметров</h3> 
</div>
---
```python
@app.route("/post/<int:id>")
```

**Типы:**

| **тип** |  **описание**  |
| :-----: | :------------: |
| string  |     строка     |
|   int   |     число      |
|  float  | число с точкой |
|  path   |      путь      |

---

<div align="center">  
<h3>Request (данные запроса)</h3> 
</div>
---
**Импорт:**

```python
from flask import request
```

**Получение данных:**

```python
request.args
```

**Пример:**

```python
name = request.args.get("name")
```

**URL:**

```
/hello?name=Alice
```

---

<div align="center">  
<h3>POST данные</h3> 
</div>
---
```python
request.form
```

**Пример:**

```python
username = request.form.get("username")
```

---

<div align="center">  
<h3>JSON данные</h3> 
</div>
---
```python
request.json
```

**Пример:**

```python
data = request.json
```

---

<div align="center">  
<h3>Response</h3> 
</div>
---
**Можно возвращать данные:**

```python
return "Hello"
```

*или JSON.*

---

<div align="center">  
<h3>JSON ответ</h3> 
</div>
---
```python
from flask import jsonify
```

**Пример:**

```python
@app.route("/api")
def api():
    return jsonify({"status": "ok"})
```

---

<div align="center">  
<h3>Templates (HTML)</h3> 
</div>
---
Flask использует **Jinja2 шаблоны**.

**Импорт:**

```python
from flask import render_template
```

**Пример:**

```python
@app.route("/")
def home():
    return render_template("index.html")
```

---

<div align="center">  
<h3>Структура проекта</h3> 
</div>
---
**Типичная структура:**

```
project/

app.py
templates/
static/
```

---

<div align="center">  
<h3>templates</h3> 
</div>
---
*Хранит HTML файлы.*

```
templates/
index.html
```

---

<div align="center">  
<h3>static</h3> 
</div>
---
**Хранит:**

- CSS
    
- JS
    
- изображения

```
static/
style.css
script.js
```

---

<div align="center">  
<h3>Redirect</h3> 
</div>
---
**Перенаправление:**

```python
from flask import redirect
```

**Пример:**

```python
return redirect("/home")
```

---

<div align="center">  
<h3>URL генерация</h3> 
</div>
---
```python
from flask import url_for
```

**Пример:**

```python
url_for("home")
```

---

<div align="center">  
<h3>Обработка ошибок</h3> 
</div>
---
```python
@app.errorhandler(404)
def not_found(error):
    return "Page not found", 404
```

---

<div align="center">  
<h3>Debug режим</h3> 
</div>
---
```python
app.run(debug=True)
```

**Позволяет:**

- авто-перезапуск
    
- просмотр ошибок

---

<div align="center">  
<h3>Flask CLI</h3> 
</div>
---
**Запуск через CLI:**

```bash
flask run
```

---

<div align="center">  
<h3>Переменная окружения</h3> 
</div>
---
**Linux / macOS:**

```bash
export FLASK_APP=app.py
```

**Windows:**

```bash
set FLASK_APP=app.py
```

---

<div align="center">  
<h3>Когда использовать Flask</h3> 
</div>
---
**Подходит для:**

- REST API
    
- backend сервисов
    
- маленьких сайтов
    
- прототипов

---

<div align="center">  
<h3>Ограничения</h3> 
</div>
---
**Flask:**

- требует дополнительных библиотек
    
- не такой полный как Django

---

<div align="center">  
<h3>Популярные расширения Flask</h3> 
</div>
---

|  **библиотека**  | **назначение** |
| :--------------: | :------------: |
| Flask-SQLAlchemy |  работа с БД   |
|   Flask-Login    |  авторизация   |
|  Flask-RESTful   |    REST API    |
|  Flask-Migrate   |    миграции    |

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Flask — лёгкий веб-фреймворк Python.

**Основные шаги:**

1. создать `Flask()`
    
2. добавить `routes`
    
3. вернуть response
    
4. запустить сервер

---

**--------------------------------------------------------**