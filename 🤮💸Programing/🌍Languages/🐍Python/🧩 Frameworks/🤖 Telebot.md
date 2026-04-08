Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🤖 Telebot — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Telebot</h3> 
</div>
---
**Telebot (pyTelegramBotAPI)** — библиотека Python для создания **Telegram-ботов** через API Telegram.

**Основные возможности:**

- обработка сообщений
    
- команды
    
- кнопки
    
- inline кнопки
    
- отправка сообщений
    
- работа с файлами
    
- middleware / handlers

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install pyTelegramBotAPI
```

**Импорт:**

```python
import telebot
```

---

<div align="center">  
<h3>Создание бота</h3> 
</div>
---
```python
import telebot

bot = telebot.TeleBot("TOKEN")
```

**Где:**

```
TOKEN — токен бота из BotFather
```

---

<div align="center">  
<h3>Запуск бота</h3> 
</div>
---
```python
bot.infinity_polling()
```

*Polling — бот постоянно проверяет новые сообщения.*

---

<div align="center">  
<h3>Обработка сообщений</h3> 
</div>
---
**Основной декоратор:**

```python
@bot.message_handler()
```

**Пример:**

```python
@bot.message_handler()
def handle_message(message):
    print(message.text)
```

---

<div align="center">  
<h3>Команды</h3> 
</div>
---
**Команды Telegram:**

```
/start
/help
/menu
```

**Пример:**

```python
@bot.message_handler(commands=["start"])
def start(message):
    bot.send_message(message.chat.id, "Hello")
```

---

<div align="center">  
<h3>Отправка сообщений</h3> 
</div>
---
**Метод:**

```
send_message()
```

**Пример:**

```python
bot.send_message(chat_id, "Hello")
```

**Где:**

```
chat_id = message.chat.id
```

---

<div align="center">  
<h3>Ответ на сообщение</h3> 
</div>
---
**Можно ответить прямо на сообщение:**

```python
bot.reply_to(message, "Hello")
```

---

<div align="center">  
<h3>Получение текста сообщения</h3> 
</div>
---
```python
message.text
```

**Пример:**

```python
@bot.message_handler()
def handler(message):
    print(message.text)
```

---

<div align="center">  
<h3>Фильтры сообщений</h3> 
</div>
---
*Можно фильтровать сообщения.*

<div align="center">  
<h4>Text</h4> 
</div>
===========================
```python
@bot.message_handler(content_types=["text"])
```

===========================

<div align="center">  
<h4>Фото</h4> 
</div>
===========================
```python
@bot.message_handler(content_types=["photo"])
```

===========================

<div align="center">  
<h4>Несколько типов</h4> 
</div>
===========================
```python
@bot.message_handler(content_types=["text","photo"])
```

===========================

---

<div align="center">  
<h3>Основные content_types</h3> 
</div>
---
```
text
photo
audio
video
document
sticker
location
contact
```

---

<div align="center">  
<h3>Работа с chat_id</h3> 
</div>
---
**Получение ID чата:**

```python
message.chat.id
```

**ID пользователя:**

```python
message.from_user.id
```

**Имя пользователя:**

```python
message.from_user.username
```

---

<div align="center">  
<h3>Reply Keyboard (кнопки)</h3> 
</div>
---
**Импорт:**

```python
from telebot import types
```

**Создание клавиатуры:**

```python
keyboard = types.ReplyKeyboardMarkup()
```

**Добавление кнопок:**

```python
button1 = types.KeyboardButton("Button 1")
button2 = types.KeyboardButton("Button 2")

keyboard.add(button1, button2)
```

**Отправка:**

```python
bot.send_message(chat_id, "Choose:", reply_markup=keyboard)
```

---

<div align="center">  
<h3>Inline Keyboard</h3> 
</div>
---
Inline кнопки работают **внутри сообщения**.

**Создание:**

```python
keyboard = types.InlineKeyboardMarkup()
```

**Кнопка:**

```python
button = types.InlineKeyboardButton(
    "Click",
    callback_data="click"
)
```

**Добавление:**

```python
keyboard.add(button)
```

**Отправка:**

```python
bot.send_message(chat_id,"Menu",reply_markup=keyboard)
```

---

<div align="center">  
<h3>Callback Query</h3> 
</div>
---
Обработка inline кнопок.

```python
@bot.callback_query_handler(func=lambda call: True)
def callback(call):
    print(call.data)
```

**Ответ:**

```python
bot.answer_callback_query(call.id)
```

---

<div align="center">  
<h3>Отправка файлов</h3> 
</div>
---

<div align="center">  
<h4>Фото</h4> 
</div>
===========================
```python
bot.send_photo(chat_id, open("photo.png","rb"))
```

===========================

<div align="center">  
<h4>Документ</h4> 
</div>
===========================
```python
bot.send_document(chat_id, open("file.pdf","rb"))
```

===========================

<div align="center">  
<h4>Видео</h4> 
</div>
===========================
```python
bot.send_video(chat_id, open("video.mp4","rb"))
```

===========================

---

<div align="center">  
<h3>Удаление сообщений</h3> 
</div>
---
```python
bot.delete_message(chat_id, message_id)
```

---

<div align="center">  
<h3>Редактирование сообщений</h3> 
</div>
---
```python
bot.edit_message_text(
    "New text",
    chat_id,
    message_id
)
```

---

<div align="center">  
<h3>Получение информации о пользователе</h3> 
</div>
---
```python
message.from_user
```

**Поля:**

```
id
username
first_name
last_name
```

---

<div align="center">  
<h3>Регистрация next step handler</h3> 
</div>
---
Используется для **диалогов**.

**Пример:**

```python
@bot.message_handler(commands=["start"])
def start(message):
    msg = bot.send_message(message.chat.id,"Your name?")
    bot.register_next_step_handler(msg, get_name)
```

**Следующий шаг:**

```python
def get_name(message):
    bot.send_message(message.chat.id,"Hello " + message.text)
```

---

<div align="center">  
<h3>Middleware</h3> 
</div>
---
**Telebot поддерживает middleware для:**

```
логирования
фильтрации
авторизации
```

*Но используется редко.*

---

<div align="center">  
<h3>Webhook</h3> 
</div>
---
Вместо polling можно использовать **webhook**.

**Polling:**

```
bot.infinity_polling()
```

**Webhook используется для:**

```
серверов
production
FastAPI / Flask
```

---

<div align="center">  
<h3>Минимальный бот</h3> 
</div>
---
```python
import telebot

bot = telebot.TeleBot("TOKEN")

@bot.message_handler(commands=["start"])
def start(message):
    bot.send_message(message.chat.id,"Hello")

bot.infinity_polling()
```

---

<div align="center">  
<h3>Структура проекта бота</h3> 
</div>
---
**Пример:**

```
bot/
│
├── main.py
├── handlers/
│
├── commands.py
├── keyboards.py
├── database.py
```

---

<div align="center">  
<h3>Самые используемые методы</h3> 
</div>
---
```
send_message()
reply_to()
send_photo()
send_document()
edit_message_text()
delete_message()
```

---

<div align="center">  
<h3>Самые используемые handlers</h3> 
</div>
---
```
message_handler
callback_query_handler
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Telebot позволяет легко создавать Telegram-ботов.

**Основные элементы:**

```
TeleBot()
message_handler
callback_query_handler
send_message()
keyboards
```

---

**--------------------------------------------------------**