Связано с [[🐍Python Frameworks]]

<div align="center">  
<h2>🧪 SQLAlchemy — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>SQLAlchemy</h3> 
</div>
---
`SQLAlchemy` — это библиотека Python для **работы с базами данных через ORM и SQL**.

**Позволяет:**

- писать SQL через Python
    
- работать с объектами вместо SQL
    
- поддерживать разные базы данных

**Поддерживаемые БД:**

- SQLite
    
- PostgreSQL
    
- MySQL
    
- MariaDB
    
- Oracle
    
- MS SQL

---

<div align="center">  
<h3>Основные компоненты SQLAlchemy</h3> 
</div>
---
**SQLAlchemy состоит из двух частей:**

|  **Компонент**  |       **Назначение**        |
| :-------------: | :-------------------------: |
| SQLAlchemy Core |        работа с SQL         |
|       ORM       | работа через Python объекты |

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install sqlalchemy
```

**Проверка:**

```python
import sqlalchemy
print(sqlalchemy.__version__)
```

---

<div align="center">  
<h3>Подключение к базе</h3> 
</div>
---
**Создание engine:**

```python
from sqlalchemy import create_engine

engine = create_engine("sqlite:///database.db")
```

---

<div align="center">  
<h3>Формат URL подключения</h3> 
</div>
---
```text
dialect+driver://username:password@host:port/database
```

**Примеры:**

*SQLite*

```python
sqlite:///db.sqlite3
```

*PostgreSQL*

```python
postgresql://user:pass@localhost/db
```

*MySQL*

```python
mysql+pymysql://user:pass@localhost/db
```

---

<div align="center">  
<h3>Engine</h3> 
</div>
---
`Engine` — основной объект подключения.

```python
engine = create_engine("sqlite:///database.db")
```

**Основные параметры:**

| **Параметр** | **Назначение**  |
| :----------: | :-------------: |
|  echo=True   | логирование SQL |
| future=True  |    новый API    |
|  pool_size   |   размер пула   |

**Пример:**

```python
engine = create_engine(
    "sqlite:///database.db",
    echo=True
)
```

---

<div align="center">  
<h3>Работа через SQLAlchemy Core</h3> 
</div>
---
**Импорт:**

```python
from sqlalchemy import text
```

**Выполнение SQL:**

```python
with engine.connect() as conn:
    result = conn.execute(text("SELECT 1"))
```

---

<div align="center">  
<h3>Получение данных</h3> 
</div>
---
```python
result = conn.execute(text("SELECT * FROM users"))

for row in result:
    print(row)
```

---

<div align="center">  
<h3>Получение одной строки</h3> 
</div>
---
```python
row = result.fetchone()
```

---

<div align="center">  
<h3>Получение всех строк</h3> 
</div>
---
```python
rows = result.fetchall()
```

---

<div align="center">  
<h3>Параметры запроса</h3> 
</div>
---
```python
conn.execute(
    text("SELECT * FROM users WHERE id=:id"),
    {"id": 1}
)
```

---

<div align="center">  
<h3>ORM (Object Relational Mapping)</h3> 
</div>
---
ORM позволяет работать с таблицами как с **Python классами**.

---

<div align="center">  
<h3>Base модель</h3> 
</div>
---
```python
from sqlalchemy.orm import declarative_base

Base = declarative_base()
```

---

<div align="center">  
<h3>Создание модели</h3> 
</div>
---
```python
from sqlalchemy import Column, Integer, String

class User(Base):

    __tablename__ = "users"

    id = Column(Integer, primary_key=True)
    name = Column(String)
    age = Column(Integer)
```

---

<div align="center">  
<h3>Создание таблиц</h3> 
</div>
---
```python
Base.metadata.create_all(engine)
```

---

<div align="center">  
<h3>Session</h3> 
</div>
---
Session — объект для работы с базой.

```python
from sqlalchemy.orm import Session

session = Session(engine)
```

---

<div align="center">  
<h3>Добавление данных</h3> 
</div>
---
```python
user = User(name="Alice", age=22)

session.add(user)

session.commit()
```

---

<div align="center">  
<h3>Добавление нескольких объектов</h3> 
</div>
---
```python
session.add_all([
    User(name="Bob"),
    User(name="John")
])
```

---

<div align="center">  
<h3>Получение данных</h3> 
</div>
---
```python
users = session.query(User).all()
```

---

<div align="center">  
<h3>Получение одной записи</h3> 
</div>
---
```python
user = session.query(User).first()
```

---

<div align="center">  
<h3>Фильтрация</h3> 
</div>
---
```python
users = session.query(User).filter(User.age > 20).all()
```

---

<div align="center">  
<h3>filter_by()</h3> 
</div>
---
**Фильтр через аргументы:**

```python
session.query(User).filter_by(name="Alice").all()
```

---

<div align="center">  
<h3>UPDATE</h3> 
</div>
---
```python
user = session.query(User).first()

user.age = 30

session.commit()
```

---

<div align="center">  
<h3>DELETE</h3> 
</div>
---
```python
session.delete(user)

session.commit()
```

---

<div align="center">  
<h3>ORDER BY</h3> 
</div>
---
```python
session.query(User).order_by(User.age).all()
```

---

<div align="center">  
<h3>LIMIT</h3> 
</div>
---
```python
session.query(User).limit(10).all()
```

---

<div align="center">  
<h3>OFFSET</h3> 
</div>
---
```python
session.query(User).offset(10).all()
```

---

<div align="center">  
<h3>AND</h3> 
</div>
---
```python
from sqlalchemy import and_

session.query(User).filter(
    and_(User.age > 20, User.age < 30)
)
```

---

<div align="center">  
<h3>OR</h3> 
</div>
---
```python
from sqlalchemy import or_

session.query(User).filter(
    or_(User.name == "Alice", User.name == "Bob")
)
```

---

<div align="center">  
<h3>Relationships</h3> 
</div>
---
Связи между таблицами.

**Типы:**

|   **Тип**    |   **Описание**   |
| :----------: | :--------------: |
|  One-to-One  |  один к одному   |
| One-to-Many  |  один ко многим  |
| Many-to-Many | многие ко многим |

---

<div align="center">  
<h3>One-to-Many пример</h3> 
</div>
---
```python
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship
```

```python
class Post(Base):

    __tablename__ = "posts"

    id = Column(Integer, primary_key=True)

    user_id = Column(Integer, ForeignKey("users.id"))
```

---

<div align="center">  
<h3>Relationship</h3> 
</div>
---
```python
class User(Base):

    posts = relationship("Post")
```

---

<div align="center">  
<h3>Session через context manager</h3> 
</div>
---
```python
from sqlalchemy.orm import Session

with Session(engine) as session:
    users = session.query(User).all()
```

---

<div align="center">  
<h3>Rollback</h3> 
</div>
---
**Отмена транзакции:**

```python
session.rollback()
```

---

<div align="center">  
<h3>Закрытие session</h3> 
</div>
---
```python
session.close()
```

---

<div align="center">  
<h3>Частые ошибки</h3> 
</div>
---
**`No module named driver`:**

Не установлен драйвер БД.

**Пример:**

```bash
pip install psycopg2
```

---
**`Table does not exist`:**

**Не выполнено:**

```python
Base.metadata.create_all(engine)
```

---
**`Session not committed`:**

*Забыли:*

```python
session.commit()
```

---

<div align="center">  
<h3>Когда использовать SQLAlchemy</h3> 
</div>
---
**Подходит для:**

- backend приложений
    
- API
    
- больших проектов
    
- сложных схем БД

---

<div align="center">  
<h3>Когда лучше sqlite3</h3> 
</div>
---
**Лучше использовать `sqlite3` если:**

- маленький проект
    
- один файл базы
    
- простой бот
    
- CLI утилита

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
SQLAlchemy — мощный инструмент для работы с БД в Python.

**Основные шаги:**

1. создать engine
    
2. создать Base
    
3. описать модели
    
4. создать таблицы
    
5. использовать Session

---

**--------------------------------------------------------**