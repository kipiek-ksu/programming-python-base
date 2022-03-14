# Python unittest

## Написание и запуск тестов в среде Pycharm
Сначала в папке с проектом создадим файл *main.py*. Определим в нём следующую функцию:
```python
def multiply(a, b):

    # проверка типов данных аргументов
    if not isinstance(a, (float, int)) or \
       not isinstance(b, (float, int)):
        error_msg = 'Недопустимый тип аргумента!'
        raise TypeError(error_msg)
    
    return a * b
```
Эта функция вычисляет произведение аргументов ```a``` и ```b```. Если хотя бы один из аргументов имеет неправильный тип данных (не [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) или [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)), то будет выброшено исключение [```TypeError```](https://docs.python.org/3/library/exceptions.html#TypeError).  

> Для тестов принято создавать отдельную папку в проекте с названием "tests". Тесты для одного класса/модуля должны располагаться в отдельном файле.  

> Для тестирования каждой функции/метода определите класс, унаследовав его от [```unittest.TestCase```](https://docs.python.org/3/library/unittest.html#unittest.TestCase). Дайте имя этому классу в стиле ```MyFunctionTest```.  

Давайте протестируем эту функцию. Сначала добавим в проект папку с названием *tests*. В этой папке нужно создать файл *multiply_test.py* - он будет содержать все тесты для нашей функции. Определим в нём класс ```MultiplyTest```, унаследовав его от [```unittest.TestCase```](https://docs.python.org/3/library/unittest.html#unittest.TestCase):
```python
import unittest
from main import multiply

class MultiplyTest(unittest.TestCase):
...
```

Этот класс будет содержать методы для тестирования нашей функции. Также в нем можно переопределить (override) методы [```setUp(self)```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.setUp) - для выполнения инструкций перед каждым тестом, [```tearDown(self)```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.tearDown) - для выполнения инструкций после каждого теста.  

> Внутри класса определяются методы-тесты для каждого тестового случая. Названия таких методов должны начинаться с *test_*.  

Сначала убедимся, что функция ```multiply(a, b)``` возвращает именно произведение чисел ```a``` и ```b```. Для этого в классе ```MultiplyTest``` определим метод ```test_positive```:
```python
class MultiplyTest(unittest.TestCase):

def test_positive(self):

    a = 4
    b = 2
    result = multiply(a, b)
    self.assertEqual(a * b, result)
```

Первыми в тесте определены переменные ```a``` и ```b``` - это входные данные. Переменная ```result``` содержит значение, возвращаемое функцией ```multiply(a, b)```. В методе [```self.assertEqual()```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertEqual) сравнивается ожидаемое значение ```a * b``` с фактическим значением переменной ```result```.

> Метод [```assertEqual(first, second, msg=None)```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertEqual) сравнивает значения аргументов ```first``` и ```second```. Если они не равны, тест упадёт с ошибкой. Необязательный параметр ```msg``` позволяет указать сообщение, которое будет выведено при падении теста.

Теперь проверим, что функция ```multiply(a, b)``` действительно бросает исключение, если её аргументы имеют неправильный (не ```int``` или ```float```) тип данных.
```python
class MultiplyTest(unittest.TestCase):

...

def test_invalid_type_left(self):

    with self.assertRaises(TypeError) as err:
        x = multiply('aaa', 4)
    error_msg = 'Недопустимый тип аргумента!'
    self.assertEqual(error_msg, err.exception.args[0])

```
Метод [```self.assertRaises()```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertRaises) - возвращает [контекстный менеджер](https://digitology.tech/posts/kontekstnye-menedzhery-v-python/) ```err```. Он сохраняет перехваченный объект исключения в своем атрибуте ```err.exception```. Это может быть полезно, если вы собираетесь дополнительно проверить сгенерированное исключение. В нашем тесте проверяется тип исключения [```TypeError```](https://docs.python.org/3/library/exceptions.html#TypeError) и текст сообщения об ошибке.

> Каждый тест следует снабдить комментарием, в котором описан тип теста (позитивный или негативный), входные данные, ожидаемое поведение (какие данные будут в результате / какую ошибку выбросит, если тест негативный).  

Добавим комментарии к каждому тесту. Вот так теперь выглядит весь файл *multiply_test.py*:
```python
import unittest
from main import multiply


class MultiplyTest(unittest.TestCase):

    def test_invalid_type_left(self):
        """
        Негативный тест - неправильный тип данных левого операнда. \n
        Будет выброшено исключение TypeError.
        """
        
        with self.assertRaises(TypeError) as err:
            x = multiply('aaa', 4)
        error_msg = 'Недопустимый тип аргумента!'
        self.assertEqual(error_msg, err.exception.args[0])
    
    def test_positive(self):
        """
        Позитивный тест - функция возвращает произведение двух чисел.
        """
        
        a = 4
        b = 2
        result = multiply(a, b)
        self.assertEqual(a * b, result)
```

> Для **"Run with Coverage"** нужен модуль *coverage.py*, если его нет в вашем проекте, IDE Pycharm предложит установить его

Теперь можно запусть тест для нашей функции. Нажмите кнопку **"Run with Coverage"** в правой верхней части экрана:

<p align=center><a href="https://imgbb.com/"><img src="https://i.ibb.co/TgP2yz2/image.png" alt="image" border="0"></a></p>

Если все тесты пройдут успешно, вы увидите что-то вроде этого:

<p align=center><a href="https://ibb.co/fCNZK5Z"><img src="https://i.ibb.co/8jxGRpG/test1.png" alt="test1" border="0"></a></br>
Какие тесты прошли успешно (или не прошли)</br></p>  
  
<p align=center><a href="https://imgbb.com/"><img src="https://i.ibb.co/Vm5M6zw/test2.png" alt="test2" border="0"></a></br>
Насколько ваш код покрыт тестами</p>  

Требования к тестам:
* Каждый тест должен использовать свой собственный набор входных данных.
* Результат выполнения одного теста не должен зависеть от других тестов.
* Тест должен падать при неправильном поведении тестируемой функции
* Тесты должны покрывать все ветки условий в теле функции и исключения, которые она может выбросить.

## Полезная информация
Видео:  
* [пересказ этой инструкции](https://www.youtube.com/watch?v=YD7aYJh3k-w), 
* [немного теории](https://www.youtube.com/watch?v=Rz4S0v7K7Ho)

Статьи и документация:
* [Краткое руководство и рекомендации](https://ru.hexlet.io/courses/advanced_python/lessons/python_testing_unittest/theory_unit) (может потребоваться VPN)
* [Еще статья](https://tirinox.ru/unit-test-python/)
* [unittest — Unit testing framework](https://docs.python.org/3/library/unittest.html) - документация Python unittest.

## Пример - программа "Калькулятор" [(исходный код)](https://github.com/Ceowyllian/Calculator)
Написать программу «Калькулятор», которая может выполнять 4 операции над числами a и b: 
* сложение ```+```
* вычитание ```-```
* умножение ```*```
* деление ```/``` 

Результат каждой операции вычисляет отдельная функция. 

Для ```+```, ```-```, ```*``` результат будет целым или вещественным числом, в зависимости от типов аргументов. 

Операция ```/``` всегда должна возвращать вещественный результат. 

Что нужно протестировать:
* Тип данных операндов – должен быть [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) или [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex). Функция должна бросать исключение, если тип неправильный.
```python
def test_invalid_type_left(self):
    """
    Негативный тест - недопустимый тип данных левого операнда. \n
    Вызовет исключение TypeError.
    """

    with self.assertRaises(TypeError) as err:
        main.validate_type('aaa', 4)
    error_msg = 'Недопустимый тип аргумента!'
    self.assertEqual(error_msg, err.exception.args[0])
```

* Тип возвращаемого результата – должен быть [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) или [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) для ```+```, ```-```, ```*``` (в зависимости от типов операндов) и [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) для деления.
* Для операции деления правый операнд не может быть нулевым. Проверить, что функция деления выбросит [```ZeroDivisionError```](https://docs.python.org/3/library/exceptions.html#ZeroDivisionError) при попытке поделить на ноль.
```python
def test_division_by_zero(self):
    """
    Негативный тест - правый операнд равен нулю. \n
    Вызовет исключение ZeroDivisionError.
    """

    a = 5
    b = 0
    with self.assertRaises(ZeroDivisionError) as err:
        main.divide(a, b)
    error_msg = 'На ноль делить нельзя!'
    self.assertEqual(error_msg, err.exception.args[0])
```

•	Позитивные тесты – функции должны выполнять именно те операции, которые описаны в задании. Сравнить результат, возвращаемый функцией и результат арифметической операции.
```python
def test_both_args_are_float(self):
    """
    Позитивный тест - операнды типа float. \n
    Результат имеет тип float.
    """

    a = 4.6
    b = 3.1
    result = main.minus(a, b)
    self.assertIsInstance(result, float)
    self.assertEqual(a - b, result)
```
