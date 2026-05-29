Связано с [[🤖 Aiogram]]

<div align="center">  
<h2>⚙️ Aiogram Base — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Aiogram Base</h3> 
</div>
---
**Aiogram** — асинхронная библиотека для создания Telegram-ботов на Python.

**Основные особенности:**

- `async / await`
    
- высокая производительность
    
- FSM (Finite State Machine)
    
- middleware
    
- filters
    
- routers
    
- webhook / polling

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install aiogram
```

**Импорт:**

```python
from aiogram import Bot, Dispatcher
```

---

<div align="center">  
<h3>Создание бота</h3> 
</div>
---
```python
from aiogram import Bot

bot = Bot(token="TOKEN")
```

---

<div align="center">  
<h3>Dispatcher</h3> 
</div>
---
**Dispatcher** — система обработки событий.

```python
from aiogram import Dispatcher

dp = Dispatcher()
```

**Dispatcher отвечает за:**

```
handlers
filters
middleware
routing
```

---

<div align="center">  
<h3>Запуск бота</h3> 
</div>
---
**Polling:**

```python
import asyncio

async def main():
    await dp.start_polling(bot)

asyncio.run(main())
```

---

<div align="center">  
<h3>Простейший бот</h3> 
</div>
---
```python
import asyncio
from aiogram import Bot, Dispatcher
from aiogram.types import Message
from aiogram.filters import Command

bot = Bot(token="TOKEN")
dp = Dispatcher()

@dp.message(Command("start"))
async def start(message: Message):
    await message.answer("Hello")

async def main():
    await dp.start_polling(bot)

asyncio.run(main())
```

---

<div align="center">  
<h3>Handlers</h3> 
</div>
---
Handler — функция, которая обрабатывает событие.

**Пример:**

```python
@dp.message()
async def handler(message: Message):
    print(message.text)
```

---

<div align="center">  
<h3>Команды</h3> 
</div>
---
**Обработка команд:**

```python
from aiogram.filters import Command
```

**Пример:**

```python
@dp.message(Command("start"))
async def start(message: Message):
    await message.answer("Hello")
```

**Команды:**

```
/start
/help
/menu
```

---

<div align="center">  
<h3>Получение текста</h3> 
</div>
---
```python
message.text
```

**ID пользователя:**

```python
message.from_user.id
```

**ID чата:**

```python
message.chat.id
```

---

<div align="center">  
<h3>Отправка сообщений</h3> 
</div>
---
**Метод:**

```python
await message.answer("Hello")
```

**Аналог:**

```python
await bot.send_message(chat_id,"Hello")
```

---

<div align="center">  
<h3>Отправка фото</h3> 
</div>
---
```python
await message.answer_photo(photo="photo.png")
```

---

<div align="center">  
<h3>Отправка файлов</h3> 
</div>
---
```python
await message.answer_document(document="file.pdf")
```

---

<div align="center">  
<h3>Reply Keyboard</h3> 
</div>
---
**Импорт:**

```python
from aiogram.types import ReplyKeyboardMarkup, KeyboardButton
```

**Создание клавиатуры:**

```python
keyboard = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="Button 1")],
        [KeyboardButton(text="Button 2")]
    ],
    resize_keyboard=True
)
```

**Отправка:**

```python
await message.answer("Choose:", reply_markup=keyboard)
```

---

<div align="center">  
<h3>Inline Keyboard</h3> 
</div>
---
**Импорт:**

```python
from aiogram.types import InlineKeyboardMarkup, InlineKeyboardButton
```

**Создание:**

```python
keyboard = InlineKeyboardMarkup(
    inline_keyboard=[
        [InlineKeyboardButton(text="Click", callback_data="click")]
    ]
)
```

**Отправка:**

```python
await message.answer("Menu", reply_markup=keyboard)
```

---

<div align="center">  
<h3>Callback Query</h3> 
</div>
---
Обработка inline кнопок.

```python
from aiogram.types import CallbackQuery
```

**Handler:**

```python
@dp.callback_query()
async def callback(call: CallbackQuery):
    print(call.data)
```

**Ответ:**

```python
await call.answer()
```

---

<div align="center">  
<h3>Filters</h3> 
</div>
---
Filters позволяют **фильтровать события**.

**Пример:**

```python
@dp.message(Command("start"))
```

**Можно фильтровать:**

```
commands
text
user
chat
callback
```

---

<div align="center">  
<h3>Router</h3> 
</div>
---
В Aiogram используется **Router** для разделения логики.

**Импорт:**

```python
from aiogram import Router
```

**Создание:**

```python
router = Router()
```

**Handler:**

```python
@router.message()
async def handler(message: Message):
    pass
```

**Подключение:**

```python
dp.include_router(router)
```

---

<div align="center">  
<h3>Middleware</h3> 
</div>
---
Middleware выполняется **до  обработчиков**.

**Используется для:**

```
логирования
авторизации
анти-спама
```

**Пример:**

```python
dp.message.middleware(MyMiddleware())
```

---

<div align="center">  
<h3>FSM (Finite State Machine)</h3> 
</div>
---
FSM используется для **диалогов**.

**Импорт:**

```python
from aiogram.fsm.state import StatesGroup, State
```

**Пример:**

```python
class Form(StatesGroup):
    name = State()
    age = State()
```

---

<div align="center">  
<h3>Webhook</h3> 
</div>
---
Aiogram поддерживает **webhook режим**.

**Используется для:**

```
FastAPI
Flask
production серверов
```

**Polling:**

```
dp.start_polling()
```

**Webhook:**

```
dp.start_webhook()
```

---

<div align="center">  
<h3>Структура проекта</h3> 
</div>
---
**Типичная архитектура:**

```
bot/
│
├── main.py
├── handlers/
│
├── keyboards/
│
├── middleware/
│
├── filters/
│
├── database/
```

---

<div align="center">  
<h3>Самые используемые методы</h3> 
</div>
---
```
message.answer()
bot.send_message()
answer_photo()
answer_document()
```

---

<div align="center">  
<h3>Основные элементы Aiogram</h3> 
</div>
---
```
Bot
Dispatcher
Router
Handlers
Filters
FSM
Middleware
```

---

<div align="center">  
<h3>Telebot vs Aiogram</h3> 
</div>
---

|    **Telebot**     |   **Aiogram**    |
| :----------------: | :--------------: |
|     синхронный     |   асинхронный    |
|       проще        |      мощнее      |
| меньше архитектуры | production ready |

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
**Aiogram** — один из самых мощных фреймворков для Telegram-ботов на Python.

**Он предоставляет:**

```
async архитектуру
router систему
FSM
middleware
filters
```

---

**--------------------------------------------------------**