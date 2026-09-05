Связано с [[🤖 Aiogram]]

<div align="center">  
<h2>🛠️ Aiogram Middleware — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Aiogram Middleware</h3> 
</div>
---
**Middleware** — это слой, который выполняется **между событием Telegram и обработчиком (handler)**.

**Схема работы:**

```
Update → Middleware → Handler
```

**Middleware может:**

- логировать события
    
- проверять права
    
- фильтровать пользователей
    
- добавлять данные в handler
    
- делать анти-спам

---

<div align="center">  
<h3>Где используется Middleware</h3> 
</div>
---
**Middleware применяют для**:

```
логирования
авторизации
админ-панели
анти-спама
работы с базой данных
```

---

<div align="center">  
<h3>Импорт Middleware</h3> 
</div>
---
```python
from aiogram import BaseMiddleware
```

---

<div align="center">  
<h3>Создание Middleware</h3> 
</div>
---
**Создаётся класс:**

```python
from aiogram import BaseMiddleware
from aiogram.types import Message
from typing import Callable, Dict, Any

class MyMiddleware(BaseMiddleware):

    async def __call__(
        self,
        handler: Callable,
        event: Message,
        data: Dict[str, Any]
    ):
        print("Message received")

        return await handler(event, data)
```

---

<div align="center">  
<h3>Параметры Middleware</h3> 
</div>
---

| **параметр** |     **описание**     |
| :----------: | :------------------: |
|  `handler`   | следующий обработчик |
|   `event`    |   событие Telegram   |
|    `data`    |   данные контекста   |

---

<div align="center">  
<h3>Подключение Middleware</h3> 
</div>
---
Middleware подключается к Dispatcher.

```python
dp.message.middleware(MyMiddleware())
```

*Теперь middleware будет работать **для всех сообщений**.*

---

<div align="center">  
<h3>Пример логирования</h3> 
</div>
---
```python
class LoggingMiddleware(BaseMiddleware):

    async def __call__(self, handler, event, data):

        print("User:", event.from_user.id)

        return await handler(event, data)
```

---

<div align="center">  
<h3>Middleware для админа</h3> 
</div>
---
Пример проверки администратора.

```python
class AdminMiddleware(BaseMiddleware):

    async def __call__(self, handler, event, data):

        admin_id = 123456

        if event.from_user.id != admin_id:
            return

        return await handler(event, data)
```

*Теперь handler будет работать **только для админа**.*

---

<div align="center">  
<h3>Передача данных в Handler</h3> 
</div>
---
Middleware может **передавать данные в handler**.

```python
class DatabaseMiddleware(BaseMiddleware):

    async def __call__(self, handler, event, data):

        data["db"] = "database_connection"

        return await handler(event, data)
```

**Handler:**

```python
@router.message()
async def handler(message: Message, db):
    print(db)
```

---

<div align="center">  
<h3>Middleware для Callback</h3> 
</div>
---
Можно подключить middleware **для callback кнопок**.

```python
dp.callback_query.middleware(MyMiddleware())
```

---

<div align="center">  
<h3>Middleware для Router</h3> 
</div>
---
Middleware можно подключить **к конкретному router**.

```python
router.message.middleware(MyMiddleware())
```

**Это полезно для:**

```
админских команд
панелей управления
специальных модулей
```

---

<div align="center">  
<h3>Pre и Post обработка</h3> 
</div>
---
**Middleware может выполнять код:**

```
до handler
после handler
```

**Пример:**

```python
class TimingMiddleware(BaseMiddleware):

    async def __call__(self, handler, event, data):

        print("Before handler")

        result = await handler(event, data)

        print("After handler")

        return result
```

---

<div align="center">  
<h3>Chain Middleware</h3> 
</div>
---
Можно подключить **несколько middleware**.

```python
dp.message.middleware(LoggingMiddleware())
dp.message.middleware(AdminMiddleware())
```

**Порядок выполнения:**

```
Logging → Admin → Handler
```

---

<div align="center">  
<h3>Пример структуры проекта</h3> 
</div>
---
```
bot/
│
├── main.py
├── handlers/
│
├── middleware/
│   ├── logging.py
│   ├── auth.py
│
├── database/
```

---

<div align="center">  
<h3>Самые частые Middleware</h3> 
</div>
---
**В реальных проектах делают:**

```
LoggingMiddleware
AuthMiddleware
DatabaseMiddleware
AntiSpamMiddleware
```

---

<div align="center">  
<h3>Минимальный пример</h3> 
</div>
---
```python
from aiogram import Bot, Dispatcher, BaseMiddleware
from aiogram.types import Message
import asyncio

bot = Bot(token="TOKEN")
dp = Dispatcher()

class LogMiddleware(BaseMiddleware):

    async def __call__(self, handler, event, data):

        print("User:", event.from_user.id)

        return await handler(event, data)

dp.message.middleware(LogMiddleware())

@dp.message()
async def handler(message: Message):
    await message.answer("Hello")

async def main():
    await dp.start_polling(bot)

asyncio.run(main())
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Middleware позволяет **перехватывать события и добавлять логику               до handler**.

**Основные возможности:**

```
логирование
авторизация
анти-спам
работа с БД
добавление данных
```

**Основной класс:**

```
BaseMiddleware
```

---

**--------------------------------------------------------**