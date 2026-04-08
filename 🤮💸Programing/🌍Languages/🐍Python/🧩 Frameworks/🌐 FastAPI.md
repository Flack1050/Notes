Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🌐 FastAPI — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>FastAPI</h3> 
</div>
---
`FastAPI` — современный Python фреймворк для создания **API и веб-сервисов**.

**Особенности:**

- очень высокая производительность
    
- автоматическая документация API
    
- асинхронная работа (`async/await`)
    
- проверка типов через Python type hints

Часто используется для:

- REST API
    
- backend сервисов
    
- микросервисов
    
- ML / AI API

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install fastapi
pip install uvicorn
```

*`uvicorn` — ASGI сервер для запуска приложения.*

---

<div align="center">  
<h3>Минимальное приложение</h3> 
</div>
---
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def home():
    return {"message": "Hello World"}
```

---

<div align="center">  
<h3>Запуск сервера</h3> 
</div>
---
```bash
uvicorn main:app --reload
```

**Объяснение:**

```
main      -> имя файла
app       -> объект FastAPI
--reload  -> авто перезапуск
```

---

<div align="center">  
<h3>Адрес сервера</h3> 
</div>
---
**По умолчанию:**

```
http://127.0.0.1:8000
```

---

<div align="center">  
<h3>Автоматическая документация</h3> 
</div>
---
FastAPI автоматически создаёт docs.

**Swagger UI:**

```
http://127.0.0.1:8000/docs
```

**Redoc:**

```
http://127.0.0.1:8000/redoc
```

*Это одна из самых сильных сторон FastAPI.*

---

<div align="center">  
<h3>Routes</h3> 
</div>
---
Маршруты определяют **URL и  обработчик**.

```python
@app.get("/")
def home():
    return {"status": "ok"}
```

---

<div align="center">  
<h3>HTTP методы</h3> 
</div>
---

| **метод** |  **декоратор**  |
| :-------: | :-------------: |
|    GET    |  `@app.get()`   |
|   POST    |  `@app.post()`  |
|    PUT    |  `@app.put()`   |
|  DELETE   | `@app.delete()` |

**Пример:**

```python
@app.post("/users")
def create_user():
    return {"created": True}
```

---

<div align="center">  
<h3>Параметры URL</h3> 
</div>
---
```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"id": user_id}
```

**URL:**

```
/users/10
```

*FastAPI автоматически проверяет тип.*

---

<div align="center">  
<h3>Query параметры</h3> 
</div>
---
**Пример:**

```
/items?limit=10
```

**Код:**

```python
@app.get("/items")
def get_items(limit: int = 10):
    return {"limit": limit}
```

---

<div align="center">  
<h3>Request Body</h3> 
</div>
---
Для POST запросов используются модели.

**Импорт:**

```python
from pydantic import BaseModel
```

---

<div align="center">  
<h3>Модель данных</h3> 
</div>
---
```python
class User(BaseModel):

    name: str
    age: int
```

---

<div align="center">  
<h3>Использование модели</h3> 
</div>
---
```python
@app.post("/users")
def create_user(user: User):
    return user
```

**FastAPI автоматически:**

- валидирует данные
    
- преобразует JSON в объект

---

<div align="center">  
<h3>Пример запроса</h3> 
</div>
---
**JSON:**

```json
{
  "name": "Alice",
  "age": 22
}
```

---

<div align="center">  
<h3>Response</h3> 
</div>
---
**Возврат данных:**

```python
return {"status": "ok"}
```

*FastAPI автоматически возвращает **JSON**.*

---

<div align="center">  
<h3>Status Code</h3> 
</div>
---
```python
from fastapi import status
```

**Пример:**

```python
@app.post("/users", status_code=201)
def create_user():
    return {"created": True}
```

---

<div align="center">  
<h3>Path параметры</h3> 
</div>
---
```python
@app.get("/posts/{post_id}")
def post(post_id: int):
    return {"post": post_id}
```

---

<div align="center">  
<h3>Optional параметры</h3> 
</div>
---
```python
from typing import Optional

@app.get("/users")
def get_user(name: Optional[str] = None):
    return {"name": name}
```

---

<div align="center">  
<h3>Async функции</h3> 
</div>
---
FastAPI поддерживает `async`.

```python
@app.get("/")
async def home():
    return {"message": "async"}
```

**Используется для:**

- API
    
- сетевых запросов
    
- баз данных

---

<div align="center">  
<h3>Dependency Injection</h3> 
</div>
---
FastAPI имеет систему зависимостей.

```python
from fastapi import Depends
```

**Пример:**

```python
def get_db():
    return "database"

@app.get("/")
def home(db=Depends(get_db)):
    return {"db": db}
```

---

<div align="center">  
<h3>Работа с формами</h3> 
</div>
---
```python
from fastapi import Form
```

**Пример:**

```python
@app.post("/login")
def login(username: str = Form()):
    return {"username": username}
```

---

<div align="center">  
<h3>Загрузка файлов</h3> 
</div>
---
```python
from fastapi import UploadFile
```

**Пример:**

```python
@app.post("/upload")
async def upload(file: UploadFile):
    return {"filename": file.filename}
```

---

<div align="center">  
<h3>Middleware</h3> 
</div>
---
Middleware выполняется **до и после запроса**.

```python
@app.middleware("http")
async def middleware(request, call_next):
    response = await call_next(request)
    return response
```

---

<div align="center">  
<h3>CORS</h3> 
</div>
---
Используется для API.

```python
from fastapi.middleware.cors import CORSMiddleware
```

**Пример:**

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"]
)
```

---

<div align="center">  
<h3>Структура проекта</h3> 
</div>
---
**Типичная структура:**

```
project/

main.py
models.py
routers/
database/
```

---

<div align="center">  
<h3>Router</h3> 
</div>
---
FastAPI поддерживает разделение маршрутов.

```python
from fastapi import APIRouter

router = APIRouter()

@router.get("/users")
def users():
    return []
```

---

<div align="center">  
<h3>Подключение router</h3> 
</div>
---
```python
app.include_router(router)
```

---

<div align="center">  
<h3>Когда использовать FastAPI</h3> 
</div>
---
**Подходит для:**

- REST API
    
- backend приложений
    
- микросервисов
    
- AI / ML API

---

<div align="center">  
<h3>Flask vs FastAPI</h3> 
</div>
---

|      **Flask**       | **FastAPI** |
| :------------------: | :---------: |
|        старый        | современный |
|      синхронный      |    async    |
| меньше автоматизации |  auto docs  |

---

<div align="center">  
<h3>Ограничения</h3> 
</div>
---
**FastAPI:**

- сложнее чем Flask
    
- требует понимания типов Python

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
`FastAPI` — современный фреймворк для создания **быстрых API на Python**.

**Основные шаги:**

1. создать `FastAPI()`
    
2. создать routes
    
3. описать модели
    
4. запустить через `uvicorn`

---

**--------------------------------------------------------**