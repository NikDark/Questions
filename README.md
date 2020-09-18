#Вопросы для собеседования
____
:white_check_mark: __Q1__
Генераторы
==========
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
:white_check_mark: Q2  
Декораторы
==
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
:white_check_mark: Q3 
Что такое mutable и immutable типы данных? Какие типы данных могут быть ключом в словаре?
==
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

_Ключом_ для словаря могут быть только неизменяемы (immutable) типы данных, потому что словарь работает с `hash()` функцией, в которую можно передать только неизменяемые параметры

---
:white_check_mark: __Q4__ 
Хэш-функция
===========
_Хэш-функция_ - это функция, которая принимает на вход какие-либо данные (например, строки) и возвращает число по некоторому заданному алгоритму  
Назначением хэш-функций является возможность помещения некоторого элемента (например, строки) в хэш-таблицу, на основе которых реализованы, например, словари и множества в Python. 
Для получения хэш-значения в Python используется встроенная функция `hash()`, работающая со встроенными типами, а для работы с пользовательскими типами, необходима реализовать метод `__hash__`
___
:white_check_mark: Q5 
Итераторы
=========
_Итератор_ - Это такой объект который хранит в себе элементы в виде очереди и к ним надо обращаться по очереди. И такие объекты возвращают элементы по одному. Функция `iter()` отработает на любом объекте, у которого есть метод `__iter__` или метод `__getitem__`
```python
In [3]: numbers = [1, 2, 3]

In [4]: i = iter(numbers)

In [5]: next(i)
Out[5]: 1

In [6]: next(i)
Out[6]: 2

In [7]: next(i)
Out[7]: 3

In [8]: next(i)
------------------------------------------------------------
StopIteration              Traceback (most recent call last)
<ipython-input-8-bed2471d02c1> in <module>()
----> 1 next(i)

StopIteration:
```
___
:white_check_mark: Q6 
X-Path
======
_Xpath_ — это язык запросов к элементам xml или xhtml документа. Также как SQL, xpath является декларативным языком запросов. 
```Xpath
.//*[@id='w3c_home_upcoming_events']/ul/li/div/p[3]
```
> Первая часть
`.//`

Она означает что мы от данной позиции спускаемся в самый низ, корень
> Вторая часть
`*[@id='w3c_home_upcoming_events']`

Она означает что мы точно обращаемся к элементу любого типа и это делает звездочка (*), но можно заменить на div, input, p и т.д.


Ну а третья часть `/ul/li/div/p[3]` означает что мы точно обращаемся как в каталогах к третьему абзацу

Чтобы получить все нечетные абзацы можно использовать функции `position()`
`/ul/li/div/p[position() mod 1]`
Так мы получим только нечетные абзацы
___
:white_check_mark: Q7 
List comprehension
==================
Конструкция, без которой легко можно обойтись, но с ней намного лучше :) Генераторы списков, как это не странно, предназначены для удобной обработки списков, к которой можно отнести и создание новых списков, и модификацию существующих.
Допустим, нам необходимо получить список нечетных чисел, не превышающих 25.
В принципе, только познакомившись с работой команды xrange решить эту проблему несложно.
```python
>>> res = []
>>> for x in xrange(1, 25, 2):
...     res.append(x)
...
>>> print res 
```
В общем-то, полученный результат — целиком нас устраивает всем, кроме длинной записи. тут-то на помощь и придет наш «сахарок». В самом простом виде, он обычно
```python
>>> res = [x for x in xrange(1, 25, 2)]
>>> print res
[1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 23]
```
###map vs list comprehension
```python
def benchmark(func):
    import time
    def wrapper():
        start = time.time()
        func()
        stop = time.time()
        print(f'time = {stop-start}')
    return wrapper

def sqr2(a):
    return a**900

@benchmark
def test_map():
    a = map(sqr2,range(100000))

@benchmark
def test_list_comprehension():
    a = [sqr2(i) for i in range(100000)]


test_map()
test_list_comprehension()

>>> time = 0.0
>>> time = 3.5345308780670166
```
> `test_map()` показала результат 0 потому что она не создавала список, она создавала генератор, а вот уже list comprehension создает список и поэтому на это уходит время
___
:white_check_mark: Q8
###LEFT, RIGHT, INNER JOIN (SQL)

Самый простой вид соединения INNER JOIN – внутреннее соединение. Этот вид джойна выведет только те строки, если условие соединения выполняется (является истинным, т.е. TRUE)
```sql
SELECT
  -- Перечисляем столбцы, которые хотим вывести
  Сотрудники.id,
  Сотрудники.Имя,
  Отделы.Наименование AS Отдел -- выводим наименование отдела и переименовываем столбец через as
FROM
  -- таблицы для соединения перечисляем в предложении from 
  Сотрудники
  -- обратите внимание, что мы не указали вид соединения, поэтому выполнится внутренний (inner) джойн
  JOIN Отделы
    -- условия соединения прописываются после ON
    -- условий может быть несколько, записанных через and, or и т.п.
    ON Сотрудники.Отдел = Отделы.id
```
Получим следующий результат:
```
id	Имя	Отдел
1	Юлия	Кухня
2	Федор	Бар
4	Светлана	Бар
```
#####LEFT JOIN и RIGHT JOIN

Левое и правое соединения еще называют внешними. Главное их отличие от внутреннего соединения в том, что строка из левой (для LEFT JOIN) или из правой таблицы (для RIGHT JOIN) попадет в результаты в любом случае. Давайте до конца определимся с тем, какая таблица левая, а какая правая.
Левая таблица та, которая идет перед написанием ключевых слов [LEFT | RIGHT| INNER] JOIN, правая таблица – после них:
```sql
SELECT
  Сотрудники.id,
  Сотрудники.Имя,
  Отделы.Наименование AS Отдел
FROM
  Сотрудники
  LEFT JOIN Отделы -- добавляем только left
    ON Сотрудники.Отдел = Отделы.id
```
Результат запроса будет следующим:
```
id	Имя	 Отдел
1	Юлия	 Кухня
2	Федор	 Бар
3	Алексей	 NULL
4	Светлана Бар
```

####Right Join
```sql
SELECT
  Сотрудники.id,
  Сотрудники.Имя,
  Отделы.Наименование AS Отдел
FROM
  Отделы
  RIGHT JOIN Сотрудники
    ON Сотрудники.Отдел = Отделы.id
```

Это мы рассмотрели пример для левого внешнего соединения. Для RIGHT JOIN будет все тоже самое, только вернутся все строки из таблицы «Отделы»:
```
id	Имя	 Отдел
1	Юлия	 Кухня
2	Федор	 Бар
4	Светлана Бар
NULL	NULL	 Администрация
```
####Full Join
Давайте посмотрим всех сотрудников и все отделы из наших тестовых таблиц:
```sql
SELECT
  Сотрудники.id,
  Сотрудники.Имя,
  Отделы.Наименование AS Отдел
FROM
  Сотрудники
  FULL JOIN Отделы
    ON Сотрудники.Отдел = Отделы.id
```
```
id	Имя	 Отдел
1	Юлия	 Кухня
2	Федор	 Бар
3	Алексей  NULL
4	Светлана Бар
NULL	NULL	 Администрация
```
___

:white_large_square: Q9 
###bs4
---
:white_check_mark: Q10 
###ООП
__Класс__ в объектно-ориентированном программировании выступает в роли чертежа для объекта. 
__Объект__ также называется __экземпляром__. Тем не менее, процесс создания объекта класса называется инициализация. В Python, чтобы создать объект класса, нам просто нужно вписать название класса, с последующими открывающимися и закрывающимися скобками.
```python
# Создаем класс Car
class Car:
 
    # создаем атрибуты класса
    name = "c200"
    make = "mercedez"
    model = 2008
 
    # создаем методы класса
    def start(self):
        print ("Заводим двигатель")
 
    def stop(self):
        print ("Отключаем двигатель")
    
# Создаем объект класса Car под названием car_a
car_a = Car()
 
# Создаем объект класса Car под названием car_b
car_b = Car()
```
####Методы
Есть тип методов, который может быть вызван напрямую при помощи имени класса. Такой метод называется `статичным методом`.

__Статичные методы__

Для объявления статического метода, вам нужно указать дескриптор @staticmethod перед названием метода, как показано ниже:
```python
class Car:
 
    @staticmethod
    def get_class_details():
        print ("Это класс Car")
 
Car.get_class_details()
```
####Магические методы
`__str__` - Вызывается, когда мы хотмим вызвать print() и передать в эту функцию наш экземпляр объекта
`__init__` - Конструктор, который вызывается при инициализации экземпляра класса


####Наследование
Основная идея __наследования__ в объектно-ориентированном программировании заключается в том, что класс может наследовать характеристики другого класса. Класс, который наследует другой класс, называется дочерним классом или производным классом, и класс, который дает наследие, называется родительским, или основным.

```python
# создаем класс Car
class Car:
 
    # создаем конструктор класса Car
    def __init__(self, model):
        # Инициализация свойств.
        self.model = model
 
    # создаем свойство модели.
    @property
    def model(self):
        return self.__model
 
    # Сеттер для создания свойств.
    @model.setter
    def model(self, model):
        if model < 2000:
            self.__model = 2000
        elif model > 2018:
            self.__model = 2018
        else:
            self.__model = model
 
    def getCarModel(self):
        return "Год выпуска модели " + str(self.model)
 
carA = Car(2088)  
print(carA.model) #2018
carA.model = 2016
print(carA.model) #2016
```
___
:white_large_square: :question: Q11 
###Django
---
:white_large_square: 
###Методологии разработки
---
:white_check_mark: 
###JSON

JSON `(JavaScript Object Notation)` это легковесный формат обмена данными
В Python для работы с JSON применяется модуль `json` 

`json.loads()` используется для парсинга данных из файла *.json. В итоге мы получаем словарь python
`json.dumps()` используя его, мы можем сконвертировать объекты Python в формат JSON
Python | JSON
--- | --- 
dict | Object
list | Array
tuple | Array
str | String
int | Number
float | Number
True | true
False | false
None | null
___

