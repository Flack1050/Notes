Связано с [[🐍Python Frameworks]]

<div align="center">  
<h2>⚡ Asyncio — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Asyncio</h3> 
</div>
---
**Asyncio** — библиотека для **асинхронного программирования** на Python.  
Позволяет запускать **несколько задач одновременно**, не блокируя основной поток.

**Схема работы:**

```text
Event Loop → Tasks → Coroutines
```

---

<div align="center">  
<h3>Основные концепции</h3> 
</div>
---
- **Event Loop** — «сердце» asyncio, выполняет задачи.
    
- **Coroutine** — функция, которая может быть приостановлена (`await`) и возобновлена.
    
- **Task** — запланированная coroutine.
    
- **Future** — объект, который содержит результат асинхронной операции.
	
- **await** — ключевое слово для ожидания coroutine или Future.

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
Asyncio встроен в Python 3.7+.

```bash
python --version
# Должно быть >= 3.7
```

---

<div align="center">  
<h3>Coroutine</h3> 
</div>
---
Coroutine создаются с `async def`.

```python
import asyncio

async def say_hello():
    print("Hello")
    await asyncio.sleep(1)
    print("World")

asyncio.run(say_hello())
```

***Примечание:** `await` можно использовать только внутри `async def`.*

---

<div align="center">  
<h3>Event Loop</h3> 
</div>
---
Event Loop управляет задачами.

```python
loop = asyncio.get_event_loop()
loop.run_until_complete(say_hello())
```

**Современный способ:**

```python
asyncio.run(say_hello())
```

---

<div align="center">  
<h3>Task</h3> 
</div>
---
Task позволяет запускать **несколько coroutine одновременно**.

```python
async def task1():
    await asyncio.sleep(1)
    print("Task 1 done")

async def task2():
    await asyncio.sleep(2)
    print("Task 2 done")

async def main():
    t1 = asyncio.create_task(task1())
    t2 = asyncio.create_task(task2())
    
    await t1
    await t2

asyncio.run(main())
```

**Результат:**

```text
Task 1 done
Task 2 done
```

---

<div align="center">  
<h3>gather</h3> 
</div>
---
`asyncio.gather` запускает несколько coroutine **параллельно**.

```python
async def main():
    await asyncio.gather(task1(), task2())
```

---

<div align="center">  
<h3>sleep</h3> 
</div>
---
**Асинхронная пауза:**

```python
await asyncio.sleep(1)
```

**Не блокирует** основной поток.

---

<div align="center">  
<h3>wait</h3> 
</div>
---
`asyncio.wait` позволяет контролировать завершение задач.

```python
done, pending = await asyncio.wait([task1(), task2()])
```

---

<div align="center">  
<h3>as_completed</h3> 
</div>
---
`asyncio.as_completed` возвращает результат **по мере выполнения задач**.

```python
for coro in asyncio.as_completed([task1(), task2()]):
    result = await coro
```

---

<div align="center">  
<h3>Future</h3> 
</div>
---
Future — объект, который будет иметь **результат позже**.

```python
future = asyncio.Future()
future.set_result(42)
print(future.result())
```

---

<div align="center">  
<h3>Exception Handling</h3> 
</div>
---
```python
try:
    await task()
except Exception as e:
    print("Error:", e)
```

---

<div align="center">  
<h3>Async Context Manager</h3> 
</div>
---
**Можно использовать `async with` для ресурсов:**

```python
class AsyncResource:
    async def __aenter__(self):
        print("Enter")
        return self

    async def __aexit__(self, exc_type, exc, tb):
        print("Exit")

async def main():
    async with AsyncResource() as r:
        print("Inside")

asyncio.run(main())
```

---

<div align="center">  
<h3>Async Iterators</h3> 
</div>
---
```python
class AsyncCounter:
    def __init__(self, limit):
        self.limit = limit
        self.count = 0

    def __aiter__(self):
        return self

    async def __anext__(self):
        if self.count >= self.limit:
            raise StopAsyncIteration
        self.count += 1
        await asyncio.sleep(0.5)
        return self.count

async def main():
    async for num in AsyncCounter(3):
        print(num)

asyncio.run(main())
```

---

<div align="center">  
<h3>Cancellation</h3> 
</div>
---
**Задачи можно отменять:**

```python
task = asyncio.create_task(task1())
task.cancel()
```

---

<div align="center">  
<h3>Timeout</h3> 
</div>
---
```python
await asyncio.wait_for(task1(), timeout=2)
```

*Если задача не успела — выбрасывается `asyncio.TimeoutError`.*

---

<div align="center">  
<h3>Thread-safe</h3> 
</div>
---
Asyncio **можно использовать вместе с threading**, но лучше держать асинхронный код внутри event loop.

---

<div align="center">  
<h3>Integration с Aiogram / FastAPI</h3> 
</div>
---
- Aiogram: все handlers — async функции, работают с asyncio.
    
- FastAPI: каждый HTTP-запрос запускается в event loop.
    
- Полезно знать `asyncio.gather`, чтобы обрабатывать **множество задач параллельно**.

---

<div align="center">  
<h3>Минимальный пример</h3> 
</div>
---
```python
import asyncio

async def hello(name):
    await asyncio.sleep(1)
    print(f"Hello {name}")

async def main():
    await asyncio.gather(
        hello("Alice"),
        hello("Bob"),
        hello("Charlie")
    )

asyncio.run(main())
```

**Результат:**

```text
Hello Alice
Hello Bob
Hello Charlie
```

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Asyncio — это **сердце асинхронного Python**.

**Основные элементы:**

```text
async def / await
Event Loop
Task
Future
asyncio.gather / wait / sleep
Cancellation / Timeout
```

---

**--------------------------------------------------------**