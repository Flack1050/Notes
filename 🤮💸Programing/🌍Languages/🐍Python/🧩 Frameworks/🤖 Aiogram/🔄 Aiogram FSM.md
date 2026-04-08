Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🔄 Aiogram FSM — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Aiogram FSM</h3> 
</div>
---
**FSM (Finite State Machine)** — система состояний, которая позволяет строить **многошаговые диалоги**.

**Пример диалога:**

```
Бот: Как тебя зовут?
Пользователь: Alex

Бот: Сколько тебе лет?
Пользователь: 20
```

*Каждый шаг — это **состояние**.*

---

<div align="center">  
<h3>Импорт FSM</h3> 
</div>
---
```python
from aiogram.fsm.state import StatesGroup, State
from aiogram.fsm.context import FSMContext
```

---

<div align="center">  
<h3>Создание States</h3> 
</div>
---
Создаётся класс состояний.

```python
class Form(StatesGroup):
    name = State()
    age = State()
```

**Здесь:**

```
name — состояние ввода имени
age — состояние ввода возраста
```

---

<div align="center">  
<h3>Запуск FSM</h3> 
</div>
---
Команда `/start` запускает первый шаг.

```python
from aiogram.filters import Command

@router.message(Command("start"))
async def start(message: Message, state: FSMContext):
    await message.answer("What is your name?")
    await state.set_state(Form.name)
```

*Теперь бот **ждёт имя пользователя**.*

---

<div align="center">  
<h3>Обработка состояния</h3> 
</div>
---
**Когда пользователь отправит сообщение:**

```python
@router.message(Form.name)
async def get_name(message: Message, state: FSMContext):

    await state.update_data(name=message.text)

    await message.answer("How old are you?")
    await state.set_state(Form.age)
```

---

<div align="center">  
<h3>Получение данных</h3> 
</div>
---
**После следующего шага:**

```python
@router.message(Form.age)
async def get_age(message: Message, state: FSMContext):

    data = await state.get_data()

    name = data["name"]
    age = message.text

    await message.answer(f"Name: {name}\nAge: {age}")

    await state.clear()
```

*`clear()` завершает FSM.*

---

<div align="center">  
<h3>Полный пример FSM</h3> 
</div>
---
```python
import asyncio
from aiogram import Bot, Dispatcher, Router
from aiogram.types import Message
from aiogram.filters import Command
from aiogram.fsm.state import StatesGroup, State
from aiogram.fsm.context import FSMContext

bot = Bot(token="TOKEN")
dp = Dispatcher()
router = Router()

class Form(StatesGroup):
    name = State()
    age = State()

@router.message(Command("start"))
async def start(message: Message, state: FSMContext):
    await message.answer("What is your name?")
    await state.set_state(Form.name)

@router.message(Form.name)
async def name(message: Message, state: FSMContext):
    await state.update_data(name=message.text)
    await message.answer("How old are you?")
    await state.set_state(Form.age)

@router.message(Form.age)
async def age(message: Message, state: FSMContext):

    data = await state.get_data()

    name = data["name"]
    age = message.text

    await message.answer(f"{name}, age {age}")

    await state.clear()

dp.include_router(router)

async def main():
    await dp.start_polling(bot)

asyncio.run(main())
```

---

<div align="center">  
<h3>Основные методы FSMContext</h3> 
</div>
---

|    **метод**    |     **описание**     |
| :-------------: | :------------------: |
|  `set_state()`  | установить состояние |
| `update_data()` |   сохранить данные   |
|  `get_data()`   |   получить данные    |
|    `clear()`    |    завершить FSM     |

---

<div align="center">  
<h3>set_state()</h3> 
</div>
---
Устанавливает состояние.

```python
await state.set_state(Form.name)
```

---

<div align="center">  
<h3>update_data()</h3> 
</div>
---
Сохраняет данные пользователя.

```python
await state.update_data(name="Alex")
```

**Можно хранить:**

```
имя
возраст
email
настройки
```

---

<div align="center">  
<h3>get_data()</h3> 
</div>
---
Получение сохранённых данных.

```python
data = await state.get_data()
```

---

<div align="center">  
<h3>clear()</h3> 
</div>
---
Завершает FSM.

```python
await state.clear()
```

*После этого бот возвращается к обычной обработке сообщений.*

---

<div align="center">  
<h3>Отмена FSM</h3> 
</div>
---
Часто делают команду `/cancel`.

```python
@router.message(Command("cancel"))
async def cancel(message: Message, state: FSMContext):

    await state.clear()

    await message.answer("Cancelled")
```

---

<div align="center">  
<h3>Проверка состояния</h3> 
</div>
---
Можно проверить текущее состояние.

```python
await state.get_state()
```

---

<div align="center">  
<h3>Где используется FSM</h3> 
</div>
---
**FSM применяют для:**

```
регистрации
форм
опросов
меню
настроек
покупок
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
├── states/
│
├── keyboards/
│
├── database/
```

**FSM классы обычно кладут в:**

```
states/
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
FSM позволяет создавать **многошаговые сценарии общения с пользователем**.

**Основные элементы:**

```
StatesGroup
State
FSMContext
```

**Основные методы:**

```
set_state()
update_data()
get_data()
clear()
```

---

**--------------------------------------------------------**