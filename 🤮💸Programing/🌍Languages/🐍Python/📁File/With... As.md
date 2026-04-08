Связано с [[🔎Languages Navigation.canvas|Навигация по ЯП]]
___
*Менеджер With... As нужен для корректного отображения ошибки `FileNotFound`*
```python
try:  
    with open("text.txt", 'r', encoding='utf-8') as file:  
        file.read()  
except FileNotFoundError:  
    print('файл не найден')
```
Также этот менеджер сам закрывает файл при любых обстоятельствах