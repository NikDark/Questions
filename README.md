# Вопросы для собеседования
____
## Оглавление-
[1. Генераторы](#Генераторы)  
[2. Декораторы](#Декораторы)  
[3. Что такое mutable и immutable типы данных? Какие типы данных могут быть ключом в словаре?](#mutable&immutable)  
[4. Хэш-функция](#hash)  
[5. Итераторы](#iter)  
[6. X-path](#xpath)  
[7. Как передаются аргументы в функцию](#arg)  
[8. Модуль copy](#copy)  
[9. А сейчас просто интересный случай](#intresting)  
[10. Множественное наследование и миксины](#inheritance)  
[11. Обработка исключений](#excepts)  
[12. List comprehension](#list)  
[13. LEFT, RIGHT and INNER JOIN(SQL)](#sql)  
[14. bs4](#bs4)  
[15. ООП](#oop)  
[16. Django](#django)  
[17. JSON](#json)  
[18. Разница между range() и xrange()](#range)  
[19. Методологии разработки](#methods)  
___
:white_check_mark: __Q1__
### Генераторы <a name="Генераторы"></a>
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
### Декораторы <a name="Декораторы"></a>
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

### Что такое mutable и immutable типы данных? Какие типы данных могут быть ключом в словаре? <a name="mutable&immutable"></a>
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

:exclamation::exclamation::exclamation::exclamation::exclamation::exclamation:Если в две переменные типа Immutable записаны два одинаковых значения, то у них будет одинаковый адрес:exclamation::exclamation::exclamation::exclamation::exclamation::exclamation:
```py
a = 123
b = 123
str1 = 'Good'
str2 = 'Good'
list1 = [1,2,3]
list2 = [1,2,3]

>> id(a) == id(b)
True
>> id(str1) == id(str2)
True
>> id(list1) == id(list2)
False
```

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
### Хэш-функция <a name="hash"></a>
_Хэш-функция_ - это функция, которая принимает на вход какие-либо данные (например, строки) и возвращает число по некоторому заданному алгоритму  
Назначением хэш-функций является возможность помещения некоторого элемента (например, строки) в хэш-таблицу, на основе которых реализованы, например, словари и множества в Python. 
Для получения хэш-значения в Python используется встроенная функция `hash()`, работающая со встроенными типами, а для работы с пользовательскими типами, необходима реализовать метод `__hash__`
___
:white_check_mark: Q5 
### Итераторы <a name="iter"></a>
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
### X-Path <a name="xpath"></a>
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
:white_check_mark:
### Как передаются аргументы в функцию <a name="arg"></a>
Есть два типа передачи аргумента в функцию
* call-by-object
* call-by-object-reference

В первом случае передаются неизменяемые типы данных, а во втором - изменяемые
___
:white_check_mark:
### Модуль copy <a name="copy"></a>
```py
x = [1,2,3]
y = x
y[0] = 4
print(x)

>>> Out: [4,2,3]
```
Обычная опирация присваивания не создает новую копию объекта, она просто создает ссылку
Для того чтобы этого избежать, используем модуль `copy`
```python
import copy
x = [1,2,3]
y = copy.copy(x) # Поверхностное копирование
y[0] = 4
print(x)
>>> Out:[1,2,3] 
```
```python
import copy
x = [1,2,3,[1,2,3]]
y = copy.deepcopy(x) # Полное копирование
y[3][0] = 4
print(x)
>>> Out:[1,2,3,[1,2,3]] 
```
Но если бы мы в последнем случае использовали обычное `copy` то внутренний массив не скопировался бы и при изменении его через `y`, `x` бы тоже изменился
___
:white_check_mark:
### А сейчас просто интересный случай <a name="intresting"></a>
#### Что напечатает?
```py
def foo(x,a[]):
    a.append(x)
    print(a)

foo(2)
foo(1,[2,3])
foo(4)

Out: [2]
Out: [2,3,1]
Out: [2,4]
```

### 2.Что напечатает?
```py
>>> var1 = True
>>> var2 = False
>>> var3 = False
>>> if var1 or var2 and var3:
...     print("True")
... else:
...     print("False")
... 
Out: True
```
Потому что приоритет сначала у `and`, а потом у `or`
#### Объединение двух словарей в Python 3.5+
```py
dic1 = {'a':1,'b':2}
dic2 = {'b':3,'c':4}
res = {**dic1,**dic2}
print(res)
Out: {'a': 1, 'b': 3, 'c': 4}
```

#### is или ==
Оператор is сравнивает адреса памяти, а оператор == сравнивает значения

#### В список можно передать функции
```py
def myFunc(a,b):
    return a+b

funcs = [myfunc]
print(funcs[0]) # Выведет типа <function at 0xmfa137123>
print(funcs[0](2,3)) # Выведет 5
```
___
:white_check_mark:
### Множественное наследование и миксины <a name="inheritance"></a>
Python поддерживает __множественное__ наследование. Можно создать несколько классов и от них создать дочерний класс, который будет наследовать эти два класса.
Например:
```py
class Auto:
    def ride(self):
        print("Riding on a ground")

class Boat:
    def swim(self):
        print("Sailing in the ocean")

class Amphibian(Auto, Boat):
    pass
 
a = Amphibian()
a.ride()
a.swim()

Out: >> Riding on a ground
Out: >> Sailing in the ocean
```

!!!ОБРАТИТЕ ВНИМАНИЕ!!!
```py
>>> a = Amphibian()
>>> isinstance(a, Auto)
True
>>> isinstance(a, Boat)
True
>>> isinstance(a, Amphibian)
True
```

#### Примеси (Миксины, Mixins)
Представим что у нас есть класс автомобиль, в котором мы реализовали функцию воспроизведения музыки. У нас все получилось, отлично! Но музыку могут воспроизводить не только автомобили, но и телефоны, и плееры. Поэтому мы создадим отдельный класс, в котором будет реализован метод воспроизведения музыки и затем наследуемся от этого класса (этот класс в котором мы реализовали метод и называется миксином)
```py
class MusicPlayerMixin:
    def play_music(self, song):
        print("Now playing: {}".format(song))

class Smartphone(MusicPlayerMixin):
    pass
 
 
class Radio(MusicPlayerMixin):
    pass
 
 
class Amphibian(Auto, Boat, MusicPlayerMixin):
   pass
```
#### Diamond problem
Допустим у нас есть класс А, который является родительским для двух классов B и C, а эти два класса являются родительскими для класса D. И у каждого родительского класса А,В и С реализован метод hi(). Вопрос, если мы создадим экземпляр класса D то какой метод hi() будет вызван?
Ответ: у первого класса, которого мы написали в скобках
Например:
```py
class A:
    def hi(self):
        print("A")

class B(A):
    def hi(self):
        print("B")

class C(A):
    def hi(self):
        print("C")

class D(B,C):
    pass

d = D()

d.hi()

Out: >> "B"
```
___
:white_check_mark: 
### Обработка исключений <a name="excepts"></a>
try\except
```py
try:
    print('10'+10)
    print(1/0)
except (TypeError,ZeroDivisionError):
    print("Неверный ввод")
```
Можно писать несколько исключений в одном except

Обычно после всех конкретных исключений идет обощенный except без указания ошибки
```py
try:
    print(1/0)
except:
    print("Исключение поймано")
finally:
    print("Хорошо")
print("Пока")
```
Блок `finally` выполняется всегда. 

Можно вызывать исключения самому с использованием `raise`
```python
a,b=int(input()),int(input())  # вводим 1 затем 0
if b==0:
    raise ZeroDivisionError
```

В данном случае будет выведена ошибка которая вызвана в блоке `try`
```py
try:
    print('1'+1)
except:
    raise
```

Также можно указать аргумент к определенному исключению в raise. Делается это с помощью дополнительных деталей исключения.

```py
raise ValueError("Несоответствующее значение")
```

Наконец, рассмотрим процесс создания собственных исключений. Для этого создадим новый класс из класса Exception. Потом его можно будет вызывать как любой другой тип исключения.
```py
class MyError(Exception):
    print("Это проблема")

raise MyError("ошибка MyError")
```
```
Traceback (most recent call last):
  File “<pyshell#179>”, line 1, in <module>
    raise MyError(“ошибка MyError”)
MyError: ошибка MyError
```


___
:white_check_mark: Q7 
### List comprehension <a name="list"></a>
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
#### map vs list comprehension 
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
### LEFT, RIGHT, INNER JOIN (SQL) <a name="sql"></a>
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
##### LEFT JOIN и RIGHT JOIN

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

#### Right Join
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
#### Full Join
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
### bs4 <a name="bs4"></a>
---
:white_check_mark: Q10 
### ООП <a name="oop"></a>
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
#### Методы
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
#### Магические методы
`__str__` - Вызывается, когда мы хотмим вызвать print() и передать в эту функцию наш экземпляр объекта
`__init__` - Конструктор, который вызывается при инициализации экземпляра класса


#### Наследование
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
### Django <a name="django"></a>
---
:white_check_mark: 
### JSON <a name="json"></a>

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

:white_check_mark: 
### Какая разница между range() и xrange() <a name="range"></a>
xrange в версии python 3.* не существует, теперь его функции выполняет range()
Раньше range() возвращал list, а xrange() генератор. Сейчас есть только range() который возвращает генератор
___
:white_check_mark: 
### Методологии разработки <a name="methods"></a>
__Методология разработки__ ПО – это система, определяющая порядок выполнения задач, методы оценки и контроля.

Разработка программного обеспечения состоит из следующих этапов:
* определение стратегии
* анализ, исследование функций которые определились на этапе определения стратегии
* проектирование – сбор модели данных
* реализация
* тестирование
* внедрение
* использование и техническая поддержка

#### Модели
##### 1. "Waterfall model" (Водопадная или каскадная)
![](https://issoft.by/wp-content/uploads/2019/06/800px-Waterfall_model.svg-768x576.png)

В этой модели все этапы рарзработки выполняются последовательно. Следующий этап не начинается, пока не завершится предыдущий.__Устаревшая модель__
##### 2. "Agile Model"
![](https://issoft.by/wp-content/uploads/2019/06/Agile_Project_Management_by_Planbox-1024x627.png)
В такой модели все этапы жизненного цикла бывают выполнены в течение одной итерации и готовы к внесению любых изменений. То есть ваш проект делится на спринты – отрезки времени, за которые должен быть получен результат, обычно от одной до четырех недель. В каждом спринте есть свой список задач, который должен быть выполнен к концу итерации.

Agile Model имеет два отдельных гибких подхода:
###### 2.1 «Scrum» (итерационная модель разработки ПО или «водоворот»)
Такая система подойдет проектам до десяти человек. Но мы бы не назвали подход эффективным, потому как требования обычно неизвестны на 50%

###### 2.1 "Kanban"
![](https://issoft.by/wp-content/uploads/2019/06/Simple-kanban-board--1024x610.jpg)
Данный подтип разработки отличается своей визуализацией жизненного цикла. Команда ориентируется на выполнение задач, которые сдаются индивидуально: задача должна пройти через все колонки – To do, In progress, Code review, In testing, Done (колонки могут быть изменены в индивидуальном порядке). Цель каждого участника команды – уменьшать количество задач в первой колонке.

##### 3. «V-Model» («шаг за шагом» или validation and verification)
![](https://issoft.by/wp-content/uploads/2019/06/Systems_Engineering_Process_rus.svg.png)
Модель разработки ПО ориентирована на то, чтобы детально проверять и тестировать продукт на первых стадиях разработки.
V-Model относят к практикам экстремального программирования.
##### 4. RAD (известная как rapid application development, быстрая разработка приложений, инкрементальная модель)
![](https://issoft.by/wp-content/uploads/2019/06/Rapid_Application_Development-1024x384.png)
Такая модель подразделяется на несколько циклов, которые составляют жизненный цикл ПО – «мульти-водопад»
##### 5. Spiral (спиральная модель)
![](https://issoft.by/wp-content/uploads/2019/06/Spiral_model_Boehm_1988.png)
Данная модель направлена на анализ оценки рисков. И отлично подойдет там, где нет права на ошибку. Также ее удобно использовать для введения новых линеек продукта и проведения исследований. Выглядит эта модель, как спираль, так как начинает оценивать риски сначала на локальных программах, пытаясь предотвратить риски, далее переходит на новый виток спирали,
##### 6. Extreme Programming (экстремальное программирование, XP)
![](https://issoft.by/wp-content/uploads/2019/06/Extreme_Programming.svg-1024x940.png)
Разработка продукта ведется короткими итерациями. Экстремальность подхода в том, что применяется первое простое решение, что создает большой риск. В методологии практикуется парное программирование и групповая разработка. Целью такой методологии является создать с заказчиком максимально доверительные отношения и значительно сократить срок разработки продукта.
##### 7. Lean (бережливая разработка ПО)
![](https://issoft.by/wp-content/uploads/2019/06/20009912451_9daf96e1e8_b.jpg)
Это не совсем методология разработки программного обеспечения. Скорее, собранные в подход принципы, нацеленные на повышение эффективности разработки продукта и улучшения рабочих процессов. Главная задача этого подхода в том, чтобы сделать проект в три раза быстрее, в три раза дешевле и в три раза чище, чем можно было бы.
В Lean есть семь принципов, которые помогают достичь цели:
• Eliminate waste;
• Amplify learning;
• Decide as late as possible;
• Deliver as fast as possible;
• Enpower the team;
• Build integrity in;
• See the whole.
___