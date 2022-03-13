# Python unittest

## Полезная информация
* Видео с примерами: [раз](https://www.youtube.com/watch?v=YD7aYJh3k-w), [два](https://www.youtube.com/watch?v=Rz4S0v7K7Ho)
* [Краткое руководство и рекомендации](https://ru.hexlet.io/courses/advanced_python/lessons/python_testing_unittest/theory_unit) (может потребоваться VPN)
* [Еще статья](https://tirinox.ru/unit-test-python/)

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
Эта функция вычисляет произведение аргументов ```a``` и ```b```. Если хотя бы один из аргументов имеет неправильный тип данных (не ```float``` или ```int```), то будет выброшено исключение ```TypeError```.  

> Для тестов принято создавать отдельную папку в проекте с названием "tests". Тесты для одного класса/модуля должны располагаться в отдельном файле.  

> Для тестирования каждой функции/метода определите класс, унаследовав его от ```unittest.TestCase```. Дайте имя этому классу в стиле ```MyFunctionTest```.  

Давайте протестируем эту функцию. Сначала добавим в проект папку с названием *tests*. В этой папке нужно создать файл *multiply_test.py* - он будет содержать все тесты для нашей функции. Определим в нём класс ```MultiplyTest```, унаследовав его от ```unittest.TestCase```:
```python
import unittest
from main import multiply

class MultiplyTest(unittest.TestCase):
...
```

Этот класс будет содержать методы для тестирования нашей функции. Также в нем можно переопределить (override) методы ```setUp(self)``` - для выполнения инструкций перед каждым тестом, ```tearDown(self)``` - для выполнения инструкций после каждого теста.  

> Внутри класса определяются методы-тесты для каждого тестового случая. Названия таких методов должны начинаться с *test_*.  

Сначала проверим, что функция ```multiply()``` действительно бросает исключение, если её аргументы имеют неправильный (не ```int``` или ```float```) тип данных.

```python
class MultiplyTest(unittest.TestCase):

def test_invalid_type_left(self):

    with self.assertRaises(TypeError) as err:
        x = multiply('aaa', 4)
    error_msg = 'Недопустимый тип аргумента!'
    self.assertEqual(error_msg, err.exception.args[0])

```

Теперь убедимся, что функция ```multiply()``` возвращает именно произведение чисел ```a``` и ```b```:

```python
class MultiplyTest(unittest.TestCase):

...

def test_positive(self):

    a = 4
    b = 2
    result = multiply(a, b)
    self.assertEqual(a * b, result)
```

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

## Требования к тесту
* Каждый тест должен использовать свой собственный набор входных данных.
* Результат выполнения одного теста не должен зависеть от других тестов.
* Тест должен падать при неправильном поведении тестируемой функции
* Тесты должны покрывать все ветки условий в теле функции и исключения, которые она может выбросить.

## Пример - программа "Калькулятор" [(исходный код)](https://github.com/Ceowyllian/Calculator)
Написать программу «Калькулятор», которая может выполнять 4 операции над числами a и b: 
* сложение (+)
* вычитание (-)
* умножение (\*)
* деление (/). 

Результат каждой операции вычисляет отдельная функция.

* Для (+), (-), (\*) результат будет целым или вещественным числом, в зависимости от типов аргументов. 
* Операция (/) всегда должна возвращать вещественный результат. 

Что нужно протестировать:
* Тип данных операндов – должен быть int или float. Функция должна бросать исключение, если тип неправильный.
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
```python
def test_invalid_type_right(self):
    """
    Негативный тест - недопустимый тип данных правого операнда. \n
    Вызовет исключение TypeError.
    """

    with self.assertRaises(TypeError) as err:
        main.validate_type(4, 'aaa')
    error_msg = 'Недопустимый тип аргумента!'
    self.assertEqual(error_msg, err.exception.args[0])
```
* Тип возвращаемого значения – должен быть int или float для ***(+), (-), (\*)*** (в зависимости от типов операндов) и float для деления.
* Для операции деления правый операнд не может быть нулевым. Проверить, что функция деления выбросит ZeroDivisionError при попытке поделить на ноль.
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
def test_both_args_are_int(self):
    """
    Позитивный тест - операнды типа int. \n
    Результат имеет тип int.
    """

    a = 4
    b = 2
    result = main.minus(a, b)
    self.assertIsInstance(result, int)
    self.assertEqual(a - b, result)
```
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
