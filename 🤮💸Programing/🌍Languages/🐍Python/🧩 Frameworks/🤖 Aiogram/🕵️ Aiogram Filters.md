Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🕵️ Aiogram Filters — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Aiogram Filters</h3> 
</div>
---
**Filters** — это механизм, который определяет **какие события должен обработать handler**.

**Пример:**

```text
Сообщение → Filter → Handler
```

Handler выполнится **только если filter совпадает**.

---

<div align="center">  
<h3>Базовый пример</h3> 
</div>
---
```python
from aiogram.filters import Command

@router.message(Command("start"))
async def start(message: Message):
    await message.answer("Hello")
```

**Filter:**

```text
Command("start")
```

*Handler сработает **только на `/start`**.*

---

<div align="center">  
<h3>Основные Filters</h3> 
</div>
---

|  **Filter**   |    **описание**     |
| :-----------: | :-----------------: |
|   `Command`   |       команды       |
|    `Text`     |        текст        |
|      `F`      | фильтрация по полям |
|  `ChatType`   |      тип чата       |
| `StateFilter` |    FSM состояние    |

---

<div align="center">  
<h3>Command Filter</h3> 
</div>
---
Используется для **команд Telegram**.

**Импорт:**

```python
from aiogram.filters import Command
```

**Пример:**

```python
@router.message(Command("start"))
async def start(message: Message):
    await message.answer("Bot started")
```

**Несколько команд:**

```python
@router.message(Command("start", "help"))
async def commands(message: Message):
    pass
```

---

<div align="center">  
<h3>Text Filter</h3> 
</div>
---
Фильтр по тексту.

```python
from aiogram.filters import Text
```

**Пример:**

```python
@router.message(Text("Hello"))
async def hello(message: Message):
    await message.answer("Hi")
```

---

<div align="center">  
<h3>F Filter (самый мощный)</h3> 
</div>
---
`F` позволяет фильтровать **по любым полям объекта**.

**Импорт:**

```python
from aiogram import F
```

**Пример:**

```python
@router.message(F.text == "hello")
async def hello(message: Message):
    pass
```

---

<div align="center">  
<h3>contains</h3> 
</div>
---
```python
@router.message(F.text.contains("hello"))
async def handler(message: Message):
    pass
```

---

<div align="center">  
<h3>startswith</h3> 
</div>
---
```python
@router.message(F.text.startswith("!"))
async def command(message: Message):
    pass
```

---

<div align="center">  
<h3>endswith</h3> 
</div>
---
```python
@router.message(F.text.endswith("?"))
async def question(message: Message):
    pass
```

---

<div align="center">  
<h3>Проверка пользователя</h3> 
</div>
---
Можно фильтровать **по пользователю**.

```python
@router.message(F.from_user.id == 123456)
async def admin(message: Message):
    pass
```

---

<div align="center">  
<h3>Проверка чата</h3> 
</div>
---
```python
@router.message(F.chat.id == 123456)
async def handler(message: Message):
    pass
```

---

<div align="center">  
<h3>ChatType Filter</h3> 
</div>
---
Фильтр по типу чата.

**Импорт:**

```python
from aiogram.filters import ChatTypeFilter
from aiogram.enums import ChatType
```

**Пример:**

```python
@router.message(ChatTypeFilter(ChatType.PRIVATE))
async def private_chat(message: Message):
    pass
```

**Типы чатов:**

```text
private
group
supergroup
channel
```

---

<div align="center">  
<h3>Content Type Filter</h3> 
</div>
---
Фильтрация по типу контента.

**Пример:**

```python
@router.message(F.photo)
async def photo(message: Message):
    pass
```

**Другие типы:**

```text
text
photo
video
audio
document
sticker
```

---

<div align="center">  
<h3>Комбинация Filters</h3> 
</div>
---
Filters можно комбинировать.

**Пример:**

```python
@router.message(Command("start"), ChatTypeFilter(ChatType.PRIVATE))
async def start(message: Message):
    pass
```

**Сработает только:**

```text
/start + private chat
```

---

<div align="center">  
<h3>Логические операторы</h3> 
</div>
---
**Можно использовать:**

```text
AND
OR
NOT
```

<div align="center">  
<h4>AND</h4> 
</div>
===========================
```python
@router.message(F.text == "hello", F.from_user.id == 123)
```

===========================

<div align="center">  
<h4>OR</h4> 
</div>
===========================
```python
@router.message(F.text.in_(["hi","hello"]))
```

===========================

<div align="center">  
<h4>NOT</h4> 
</div>
===========================
```python
@router.message(~F.text.startswith("/"))
```

===========================

---

<div align="center">  
<h3>Filters для Callback</h3> 
</div>
---
Фильтрация inline кнопок.

```python
@router.callback_query(F.data == "menu")
async def menu(call: CallbackQuery):
    pass
```

---

<div align="center">  
<h3>Custom Filters</h3> 
</div>
---
Можно создавать **свои фильтры**.

```python
from aiogram.filters import BaseFilter
```

**Пример:**

```python
class IsAdmin(BaseFilter):

    async def __call__(self, message: Message):

        return message.from_user.id == 123456
```

**Использование:**

```python
@router.message(IsAdmin())
async def admin_panel(message: Message):
    pass
```

---

<div align="center">  
<h3>Пример использования Filters</h3> 
</div>
---
```python
from aiogram import F

@router.message(F.text.contains("python"))
async def python(message: Message):
    await message.answer("Python detected")
```

---

<div align="center">  
<h3>Самые используемые Filters</h3> 
</div>
---
```text
Command
F.text
F.photo
F.data
ChatTypeFilter
```

---

<div align="center">  
<h3>Где используются Filters</h3> 
</div>
---
**Filters применяются для:**

```text
команд
админов
типов сообщений
callback кнопок
типов чатов
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Filters позволяют **точно управлять обработкой событий**.

**Основные фильтры:**

```text
Command
Text
F
ChatTypeFilter
```

**Самый мощный инструмент:**

```text
F filter
```

---

**--------------------------------------------------------**