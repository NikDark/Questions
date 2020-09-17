#Вопросы для собеседования
____
:white_check_mark: **Q1** Генераторы  
*Генераторы* — это функции, которые можно приостанавливать и возобновлять во время их выполнения, при этом они возвращают объект, который можно итерировать.
```python
>>> def countdown(num):
...     print('Starting')
...     while num > 0:
...         yield num
...         num -= 1
...
>>> val = countdown(5)
>>> val
<generator object countdown at 0x10213aee8>
>>> next(val)
Starting
5
>>> next(val)
4
>>> next(val)
3
>>> next(val)
2
>>> next(val)
1
>>> next(val)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
Обратите внимание на круглые скобки по обеим сторонам второй строки, обозначающие выражение генератора, которое, по большей части, делает то же самое, что и генератор списка, но делает это лениво
```python
>>> import sys
>>> g = (i * 2 for i in range(10000) if i % 3 == 0 or i % 5 == 0)
>>> print(sys.getsizeof(g))
72
>>> l = [i * 2 for i in range(10000) if i % 3 == 0 or i % 5 == 0]
>>> print(sys.getsizeof(l))
38216
```
Генераторы идеально подходят для чтения большого количества больших файлов, поскольку они выдают данные по одному фрагменту за раз, независимо от размера входного потока. 
Генераторы отлично работают и для рекурсивного парсинга веб-страниц
___
:white_check_mark: Q2  __Декораторы__  
_Декоратор_ — это функция, которая позволяет обернуть другую функцию для расширения её функциональности без непосредственного изменения её кода.  
```python
def benchmark(func):
    import time
    
    def wrapper(*args, **kwargs):
        start = time.time()
        return_value = func(*args, **kwargs)
        end = time.time()
        print('[*] Время выполнения: {} секунд.'.format(end-start))
        return return_value
    return wrapper

@benchmark
def fetch_webpage(url):
    import requests
    webpage = requests.get(url)
    return webpage.text

webpage = fetch_webpage('https://google.com')
print(webpage)
```
```html
[*] Время выполнения: 1.4475083351135254 секунд.
<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage"........
```
> Декоратор принимает функцию в качестве аргумента и возвращает функцию.  

---
:white_check_mark: Q3  __Что такое mutable и immutable типы данных?   
Какие типы данных могут быть ключом в словаре?__

_Mutable_   
 Под изменяемыми понимают типы, объекты которых могут быть изменены на месте, как, например, список, который имеет модифицирующие его методы list.remove, list.append,...
* list
* dict
* set
* object

_Immutable_   
Напротив, у неизменяемых объектов отсутствует модифицирующие их методы. Например, в следующем примере будет создан новый объект:
```python
x = 1
x += 1 # x - теперь ссылается на новый объект, 2.
```
* boolean
* int
* float
* string
* tuple
* frozenset

Если в функцию, которая изменяет аргумент, передать mutable параметр, то переменная которая выступала параметром будет изменена и вне функции
```python
#!/usr/bin/env python3
def change_me( mylist):
    """
    This changes a passed object.
    """
    mylist += [1,2,3,4]
    print("Inside the function: ", mylist) # [10,20,30,1,2,3,4]

my_list= [10,20,30]
change_me(my_list)
print("Outside the function: ", mylist) # [10,20,30,1,2,3,4]
```

А если передадим immutable параметр, то переменная не будет изменена вне функции
```python
#!/usr/bin/env python3
def try_change_me( mytuple):
    """This does not change a passed object."""
    mytuple += (1,2,3,4)
    print("Inside the function: ", mytuple) # (10,20,30,1,2,3,4)
    
my_tuple= (10,20,30)
try_change_me(my_tuple)
print("Outside the function: ", my_tuple) # (10,20,30)
```

_Ключом_ для словаря могут быть только неизменяемы (immutable) типы данных, потому что словарь работает с hash() функцией, в которую можно передать только неизменяемые параметры

---
:white_large_square: Q4 __Хэш-функция__  
:white_large_square: Q5 __Итераторы__  
:white_large_square: Q6 __X-Path__  
:white_large_square: Q7 __LEFT, RIGHT, INNER JOIN (SQL)__  
:white_large_square: Q8 __bs4__  
:white_large_square: Q9 __List comprehension__  
:white_large_square: Q10 __ООП__
:white_large_square: :question: Q11 __Django__  
