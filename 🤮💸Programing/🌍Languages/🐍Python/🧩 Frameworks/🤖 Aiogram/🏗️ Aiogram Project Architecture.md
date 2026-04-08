Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🏗️ Aiogram Project Architecture — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Aiogram Project Architecture</h3> 
</div>
---
Когда бот становится большим, **всё в одном файле превращается в хаос**.

**Правильная архитектура разделяет:**

```
handlers
routers
services
database
keyboards
middlewares
states
```

**Это делает проект:**

- удобным
    
- масштабируемым
    
- читаемым

---

<div align="center">  
<h3>Базовая структура проекта</h3> 
</div>
---
**Типичная структура:**

```
bot/
│
├── main.py
│
├── handlers/
│   ├── start.py
│   ├── admin.py
│   └── user.py
│
├── routers/
│   └── main_router.py
│
├── keyboards/
│   ├── inline.py
│   └── reply.py
│
├── middleware/
│   ├── logging.py
│   └── auth.py
│
├── filters/
│
├── states/
│   └── form.py
│
├── services/
│   └── user_service.py
│
├── database/
│   └── db.py
```

---

<div align="center">  
<h3>main.py</h3> 
</div>
---
Главный файл запуска бота.

**Пример:**

```python
import asyncio
from aiogram import Bot, Dispatcher
from routers.main_router import router

bot = Bot(token="TOKEN")
dp = Dispatcher()

dp.include_router(router)

async def main():
    await dp.start_polling(bot)

asyncio.run(main())
```

*Этот файл должен быть **максимально коротким**.*

---

<div align="center">  
<h3>Handlers</h3> 
</div>
---
Handlers — это функции, которые **обрабатывают события**.

**Пример:**

```
handlers/
    start.py
```

```python
from aiogram import Router
from aiogram.types import Message
from aiogram.filters import Command

router = Router()

@router.message(Command("start"))
async def start(message: Message):
    await message.answer("Hello")
```

---

<div align="center">  
<h3>Routers</h3> 
</div>
---
Router объединяет handlers.

**Пример:**

```
routers/main_router.py
```

```python
from aiogram import Router
from handlers.start import router as start_router
from handlers.admin import router as admin_router

router = Router()

router.include_router(start_router)
router.include_router(admin_router)
```

---

<div align="center">  
<h3>Keyboards</h3> 
</div>
---
Все кнопки хранятся отдельно.

```
keyboards/
```

**Пример:**

```python
from aiogram.types import InlineKeyboardMarkup, InlineKeyboardButton

menu = InlineKeyboardMarkup(
    inline_keyboard=[
        [InlineKeyboardButton(text="Profile", callback_data="profile")]
    ]
)
```

---

<div align="center">  
<h3>States</h3> 
</div>
---
FSM состояния.

```
states/
```

**Пример:**

```python
from aiogram.fsm.state import StatesGroup, State

class Form(StatesGroup):
    name = State()
    age = State()
```

---

<div align="center">  
<h3>Services</h3> 
</div>
---
Services содержат **бизнес-логику**.

```
services/
```

**Пример:**

```python
def create_user(name, age):
    print("User created")
```

*Handlers вызывают **services**, а не пишут логику напрямую.*

---

<div align="center">  
<h3>Database</h3> 
</div>
---
Модуль работы с базой.

```
database/
```

**Пример:**

```python
import sqlite3

conn = sqlite3.connect("db.sqlite")
```

**Или:**

- SQLAlchemy
    
- asyncpg
    
- PostgreSQL

---

<div align="center">  
<h3>Middleware</h3> 
</div>
---
```
middleware/
```

**Примеры middleware:**

```
logging
auth
antispam
database
```

---

<div align="center">  
<h3>Filters</h3> 
</div>
---
**Custom filters:**

```
filters/
```

**Пример:**

```python
class IsAdmin(BaseFilter):
    async def __call__(self, message: Message):
        return message.from_user.id == 123456
```

---

<div align="center">  
<h3>Большая архитектура</h3> 
</div>
---
**Production-бот может выглядеть так:**

```
bot/
│
├── main.py
│
├── core/
│   ├── config.py
│   └── bot.py
│
├── handlers/
│
├── routers/
│
├── services/
│
├── database/
│
├── middleware/
│
├── filters/
│
├── keyboards/
│
├── states/
│
├── utils/
```

---

<div align="center">  
<h3>Core модуль</h3> 
</div>
---
```
core/
```

**Обычно содержит:**

```
config.py
settings.py
env loader
```

**Пример:**

```python
from dotenv import load_dotenv
import os

load_dotenv()

TOKEN = os.getenv("BOT_TOKEN")
```

---

<div align="center">  
<h3>Правило архитектуры</h3> 
</div>
---
**Главное правило:**

```
Handler → Service → Database
```

*Handlers **не должны работать напрямую с БД**.*

---

<div align="center">  
<h3>Плохая архитектура</h3> 
</div>
---
**❌ Всё в одном файле:**

```
bot.py (3000 строк)
```

---

<div align="center">  
<h3>Хорошая архитектура</h3> 
</div>
---
**✔ Разделение:**

```
handlers
services
database
```

---

<div align="center">  
<h3>Минимальная архитектура</h3> 
</div>
---
**Для небольших ботов:**

```
bot/
│
├── main.py
├── handlers/
├── keyboards/
├── states/
```

---

<div align="center">  
<h3>Production архитектура</h3> 
</div>
---
**Для больших ботов:**

```
bot/
│
├── core
├── handlers
├── routers
├── middleware
├── services
├── database
├── keyboards
├── states
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
**Правильная архитектура Aiogram делает код:**

```
масштабируемым
читаемым
поддерживаемым
```

**Основные модули:**

```
handlers
routers
services
database
middlewares
states
keyboards
```

---

**--------------------------------------------------------**