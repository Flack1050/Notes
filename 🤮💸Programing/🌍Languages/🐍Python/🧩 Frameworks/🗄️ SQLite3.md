Связано с [[🔎 Frameworks Navigation.canvas|🔎 Frameworks Navigation]]

<div align="center">  
<h2>🗄️ SQLite3 — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>SQLite3</h3> 
</div>
---
`sqlite3` — стандартная библиотека Python для работы с **SQLite базами данных**.

SQLite — это **встроенная (embedded) SQL база**, которая хранится в **одном файле**.

**Особенности:**

- не требует сервера
    
- работает из файла
    
- очень лёгкая
    
- входит в стандартную библиотеку Python

---

<div align="center">  
<h3>Импорт библиотеки</h3> 
</div>
---
```python
import sqlite3
```

---

<div align="center">  
<h3>Создание / подключение к базе</h3> 
</div>
---
```python
conn = sqlite3.connect("database.db")
```

Если файл не существует — он **создаётся автоматически**.

---

<div align="center">  
<h3>Подключение к временной базе (в памяти)</h3> 
</div>
---
```python
conn = sqlite3.connect(":memory:")
```

**Такая база:**

- хранится в RAM
    
- удаляется после завершения программы

---

<div align="center">  
<h3>Cursor</h3> 
</div>
---
Cursor используется для **выполнения SQL команд**.

```python
cursor = conn.cursor()
```

---

<div align="center">  
<h3>Выполнение SQL</h3> 
</div>
---
```python
cursor.execute("SQL запрос")
```

**Пример:**

```python
cursor.execute("SELECT * FROM users")
```

---

<div align="center">  
<h3>Создание таблицы</h3> 
</div>
---
```python
cursor.execute("""
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT,
    age INTEGER
)
""")
```

---

<div align="center">  
<h3>Сохранение изменений</h3> 
</div>
---
После изменения базы нужно вызвать:

```python
conn.commit()
```

Без этого изменения **не сохранятся**.

---

<div align="center">  
<h3>Закрытие соединения</h3> 
</div>
---
```python
conn.close()
```

---

<div align="center">  
<h3>Основные методы Cursor</h3> 
</div>
---
**execute():**

Выполняет один SQL запрос.

```python
cursor.execute("SELECT * FROM users")
```

---

<div align="center">  
<h3>executemany()</h3> 
</div>
---
Выполняет запрос **несколько раз**.

```python
cursor.executemany(
    "INSERT INTO users(name, age) VALUES(?, ?)",
    [
        ("Alice", 20),
        ("Bob", 25),
        ("John", 30)
    ]
)
```

---

<div align="center">  
<h3>executescript()</h3> 
</div>
---
Выполняет **несколько SQL команд**.

```python
cursor.executescript("""
CREATE TABLE users(id INTEGER);
CREATE TABLE posts(id INTEGER);
""")
```

---

<div align="center">  
<h3>Получение данных</h3> 
</div>
---
## fetchone()

Возвращает **одну строку**.

```python
cursor.fetchone()
```

---

<div align="center">  
<h3>fetchmany()</h3> 
</div>
---
Возвращает **несколько строк**.

```python
cursor.fetchmany(5)
```

---

<div align="center">  
<h3>fetchall()</h3> 
</div>
---
Возвращает **все строки**.

```python
cursor.fetchall()
```

---

<div align="center">  
<h3>Placeholder (защита от SQL-инъекций</h3> 
</div>
---
SQLite использует `?`.

```python
cursor.execute(
    "INSERT INTO users(name, age) VALUES (?, ?)",
    ("Alice", 22)
)
```

**Никогда не делать:**

```python
cursor.execute(f"INSERT INTO users VALUES {name}")
```

---

<div align="center">  
<h3>Типы данных SQLite</h3> 
</div>
---

| **Тип** |  **Описание**   |
| :-----: | :-------------: |
|  NULL   | пустое значение |
| INTEGER |   целое число   |
|  REAL   |  дробное число  |
|  TEXT   |     строка      |
|  BLOB   | бинарные данные |

---

<div align="center">  
<h3>PRIMARY KEY</h3> 
</div>
---
```sql
id INTEGER PRIMARY KEY
```

**Особенности:**

- уникальный ключ
    
- автоматически увеличивается

---

<div align="center">  
<h3>AUTOINCREMENT</h3> 
</div>
---
```sql
id INTEGER PRIMARY KEY AUTOINCREMENT
```

Гарантирует **монотонное увеличение id**.

---

<div align="center">  
<h3>INSERT</h3> 
</div>
---
**Добавление данных:**

```python
cursor.execute(
    "INSERT INTO users(name, age) VALUES (?, ?)",
    ("Alice", 21)
)
```

---

<div align="center">  
<h3>SELECT</h3> 
</div>
---
**Получение данных:**

```python
cursor.execute("SELECT * FROM users")
rows = cursor.fetchall()
```

---

<div align="center">  
<h3>UPDATE</h3> 
</div>
---
**Изменение данных:**

```python
cursor.execute(
    "UPDATE users SET age=? WHERE name=?",
    (25, "Alice")
)
```

---

<div align="center">  
<h3>DELETE</h3> 
</div>
---
**Удаление строк:**

```python
cursor.execute(
    "DELETE FROM users WHERE name=?",
    ("Alice",)
)
```

---

<div align="center">  
<h3>Пример полного кода</h3> 
</div>
---
```python
import sqlite3

conn = sqlite3.connect("users.db")

cursor = conn.cursor()

cursor.execute("""
CREATE TABLE IF NOT EXISTS users(
    id INTEGER PRIMARY KEY,
    name TEXT,
    age INTEGER
)
""")

cursor.execute(
    "INSERT INTO users(name, age) VALUES (?, ?)",
    ("Alice", 22)
)

conn.commit()

cursor.execute("SELECT * FROM users")

print(cursor.fetchall())

conn.close()
```

---

<div align="center">  
<h3>Работа с Row объектами</h3> 
</div>
---
Можно получать строки как **словарь**.

```python
conn.row_factory = sqlite3.Row
```

**Теперь:**

```python
row["name"]
```

---

<div align="center">  
<h3>Проверка существования таблицы</h3> 
</div>
---
```python
cursor.execute("""
SELECT name FROM sqlite_master
WHERE type='table'
""")
```

---

<div align="center">  
<h3>Удаление таблицы</h3> 
</div>
---
```python
cursor.execute("DROP TABLE users")
```

---

<div align="center">  
<h3>Транзакции</h3> 
</div>
---
SQLite поддерживает транзакции.

```python
conn.commit()
conn.rollback()
```

*Rollback отменяет изменения.*

---

<div align="center">  
<h3>Контекстный менеджер</h3> 
</div>
---
Можно использовать `with`.

```python
import sqlite3

with sqlite3.connect("db.db") as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT 1")
```

Соединение **закроется автоматически**.

---

<div align="center">  
<h3>Частые ошибки</h3> 
</div>
---
**`database is locked:`**

*База используется другим процессом.*

---
**`no such table`:**

*Таблица не существует.*

---
**`forgetting commit`:**

*Без `commit()` изменения не сохраняются.*

---

<div align="center">  
<h3>Когда использовать SQLite</h3> 
</div>
---
**Подходит для:**

- CLI программ
    
- ботов
    
- локальных приложений
    
- прототипов
    
- маленьких сервисов

---

<div align="center">  
<h3>Ограничения SQLite</h3> 
</div>
---
**SQLite не подходит для:**

- высоконагруженных серверов
    
- большого количества одновременных пользователей

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
`sqlite3` — простая встроенная база данных Python.

**Основные шаги:**

1. подключиться к базе
    
2. создать cursor
    
3. выполнить SQL
    
4. commit
    
5. закрыть соединение

---

**--------------------------------------------------------**