Связано с [[🐍Python Frameworks]]

<div align="center">  
<h2>🎮 PyGame — Documentation</h2> 
</div>
**--------------------------------------------------------**

<div align="center">  
<h3>Pygame</h3> 
</div>
---
`Pygame` — библиотека Python для **создания 2D игр**.

**Позволяет работать с:**

- окнами
    
- графикой
    
- звуком
    
- клавиатурой
    
- мышью
    
- спрайтами

**Используется для:**

- 2D игр
    
- прототипов игр
    
- обучения game development

---

<div align="center">  
<h3>Установка</h3> 
</div>
---
```bash
pip install pygame
```

**Проверка:**

```python
import pygame
print(pygame.__version__)
```

---

<div align="center">  
<h3>Инициализация Pygame</h3> 
</div>
---
Перед использованием нужно инициализировать библиотеку.

```python
import pygame

pygame.init()
```

---

<div align="center">  
<h3>Создание окна</h3> 
</div>
---
```python
screen = pygame.display.set_mode((800, 600))
```

**Параметры:**

| **параметр** | **описание** |
| :----------: | :----------: |
|    ширина    | ширина окна  |
|    высота    | высота окна  |

---

<div align="center">  
<h3>Название окна</h3> 
</div>
---
```python
pygame.display.set_caption("My Game")
```

---

<div align="center">  
<h3>Основной игровой цикл</h3> 
</div>
---
Каждая игра использует **game loop**.

```python
running = True

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
```

---

<div align="center">  
<h3>Закрытие игры</h3> 
</div>
---
```python
pygame.quit()
```

---

<div align="center">  
<h3>FPS (кадры в секунду)</h3> 
</div>
---
Для контроля скорости игры используется `Clock`.

```python
clock = pygame.time.Clock()
```

**Ограничение FPS:**

```python
clock.tick(60)
```

---

<div align="center">  
<h3>Цвета</h3> 
</div>
---
Цвет задаётся через **RGB**.

```python
(255, 255, 255)
```

| **цвет** |    **RGB**    |
| :------: | :-----------: |
|  белый   | (255,255,255) |
|  чёрный  |    (0,0,0)    |
| красный  |   (255,0,0)   |
| зелёный  |   (0,255,0)   |
|  синий   |   (0,0,255)   |

---

<div align="center">  
<h3>Очистка экрана</h3> 
</div>
---
```python
screen.fill((0,0,0))
```

---

<div align="center">  
<h3>Обновление экрана</h3> 
</div>
---
```python
pygame.display.update()
```

**или:**

```python
pygame.display.flip()
```

---

<div align="center">  
<h3>Рисование фигур</h3> 
</div>
---

<div align="center">  
<h4>Прямоугольник</h4> 
</div>
===========================
```python
pygame.draw.rect(screen, (255,0,0), (100,100,50,50))
```

**Параметры:**

```
surface
color
(x,y,width,height)
```

===========================

<div align="center">  
<h4>Круг</h4> 
</div>
===========================
```python
pygame.draw.circle(screen, (0,255,0), (200,200), 40)
```

===========================

<div align="center">  
<h4>Линия</h4> 
</div>
===========================
```python
pygame.draw.line(screen, (255,255,255), (0,0), (300,300), 3)
```

===========================

---

<div align="center">  
<h3>Обработка событий</h3> 
</div>
---
**Получение событий:**

```python
pygame.event.get()
```

**Типы событий:**

|   **событие**   |    **описание**    |
| :-------------: | :----------------: |
|      QUIT       |   закрытие окна    |
|     KEYDOWN     |  нажатие клавиши   |
|      KEYUP      | отпускание клавиши |
| MOUSEBUTTONDOWN |     клик мыши      |

---

<div align="center">  
<h3>Нажатие клавиш</h3> 
</div>
---
```python
if event.type == pygame.KEYDOWN:
    print("Key pressed")
```

**Проверка клавиши:**

```python
if event.key == pygame.K_SPACE:
    print("SPACE")
```

---

<div align="center">  
<h3>Получение состояния клавиатуры</h3> 
</div>
---
```python
keys = pygame.key.get_pressed()

if keys[pygame.K_w]:
    print("W pressed")
```

---

<div align="center">  
<h3>Мышь</h3> 
</div>
---
**Позиция мыши:**

```python
pygame.mouse.get_pos()
```

**Клик мыши:**

```python
pygame.mouse.get_pressed()
```

---

<div align="center">  
<h3>Загрузка изображения</h3> 
</div>
---
```python
image = pygame.image.load("player.png")
```

**Отрисовка:**

```python
screen.blit(image, (100,100))
```

---

<div align="center">  
<h3>Загрузка звука</h3> 
</div>
---
```python
sound = pygame.mixer.Sound("sound.wav")
sound.play()
```

---

<div align="center">  
<h3>Музыка</h3> 
</div>
---
```python
pygame.mixer.music.load("music.mp3")
pygame.mixer.music.play(-1)
```

`-1` означает **бесконечное повторение**.

---

<div align="center">  
<h3>Шрифты и текст</h3> 
</div>
---
**Создание шрифта:**

```python
font = pygame.font.Font(None, 36)
```

**Создание текста:**

```python
text = font.render("Hello", True, (255,255,255))
```

**Отрисовка:**

```python
screen.blit(text, (100,100))
```

---

<div align="center">  
<h3>Пример простой программы</h3> 
</div>
---
```python
import pygame

pygame.init()

screen = pygame.display.set_mode((800,600))
pygame.display.set_caption("Pygame")

clock = pygame.time.Clock()

running = True

while running:

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.fill((0,0,0))

    pygame.draw.rect(screen, (255,0,0), (200,200,50,50))

    pygame.display.update()

    clock.tick(60)

pygame.quit()
```

---

<div align="center">  
<h3>Основные модули Pygame</h3> 
</div>
---

|   **модуль**   | **описание** |
| :------------: | :----------: |
| pygame.display |     окно     |
|  pygame.draw   |  рисование   |
|  pygame.event  |   события    |
|  pygame.image  | изображения  |
|  pygame.mixer  |     звук     |
|  pygame.font   |    текст     |
|  pygame.time   |    таймер    |

---

<div align="center">  
<h3>Когда использовать Pygame</h3> 
</div>
---
**Подходит для:**

- 2D игр
    
- обучения game dev
    
- прототипов

---

<div align="center">  
<h3>Ограничения</h3> 
</div>
---
**Pygame:**

- не подходит для **3D игр**
    
- не подходит для **больших AAA проектов**

---

<div align="center">  
<h3>Итог</h3> 
</div>
---
Pygame — простая библиотека для **создания 2D игр на Python**.

**Основной процесс:**

1. `pygame.init()`
    
2. создать окно
    
3. сделать game loop
    
4. обрабатывать события
    
5. рисовать объекты
    
6. обновлять экран

---

**--------------------------------------------------------**