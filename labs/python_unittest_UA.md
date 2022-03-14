# Python unittest

## Написання та запуск тестів у середовищі Pycharm
Спочатку у директорії з проєктом створимо файл *main.py*. Визначимо у ньому наступну функцію:
```python
def multiply(a, b):

    # перевірка типів даних аргументів
    if not isinstance(a, (float, int)) or \
       not isinstance(b, (float, int)):
        error_msg = 'Неприпустимий тип аргументу!'
        raise TypeError(error_msg)
    
    return a * b
```
Ця функція обчислює добуток аргументів ```a``` і ```b```. Якщо хоча б один із аргументів має неправильний тип даних (не [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) або [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)), то буде згенеровано виключення [```TypeError```](https://docs.python.org/3/library/exceptions.html#TypeError).  

> Для тестів прийнято створювати окрему папку у проекті під назвою "tests". Тести для одного класу/модуля повинні розташовуватися в окремому файлі.  

> Для тестування кожної функції/методу визначте клас, успадкувавши його від [```unittest.TestCase```](https://docs.python.org/3/library/unittest.html#unittest.TestCase). Дайте ім'я цьому класу на кшталт ```MyFunctionTest```.  

Давайте протестуємо цю функцію. Спочатку додамо до проєкту директорію з назвою *tests*. У цій директорії потрібно створити файл *multiply_test.py* - він буде містити всі тести для нашої функції. Визначимо у ньому клас ```MultiplyTest```, успадкувавши його від [```unittest.TestCase```](https://docs.python.org/3/library/unittest.html#unittest.TestCase):
```python
import unittest
from main import multiply

class MultiplyTest(unittest.TestCase):
...
```

Цей клас міститиме методи для тестування нашої функції. Також у ньому можна перевизначити (override) методи [```setUp(self)```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.setUp) - для виконання інструкцій перед кожним тестом, [```tearDown(self)```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.tearDown) - виконання інструкцій після кожного тесту.  

> Усередині класу визначаються методи-тести для кожного тестового випадку. Назви таких методів повинні починатися з *test_*.  

Спочатку переконаємось, що функція ```multiply(a, b)``` повертає саме добуток чисел ```a``` і ```b```. Для цього у класі ```MultiplyTest``` визначимо метод ```test_positive```:
```python
class MultiplyTest(unittest.TestCase):

def test_positive(self):

    a = 4
    b = 2
    result = multiply(a, b)
    self.assertEqual(a * b, result)
```

Першими у тесті визначено змінні ```a``` і ```b``` - це вхідні дані. Змінна ```result``` містить значення, що повертається функцією ```multiply(a, b)```. У методі [```self.assertEqual()```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertEqual) порівнюється очікуване значення ```a * b``` з фактичним значенням змінної ```result```.

> Метод [```assertEqual(first, second, msg=None)```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertEqual) порівнює значення аргументів ```first``` і ```second```. Якщо вони не рівні, тест завершить роботу з помилкою. Необов'язковий параметр ```msg``` дозволяє вказати повідомлення, яке буде виведено під час падіння тесту.

Тепер перевіримо, що функція ```multiply(a, b)``` справді генерує виняток, якщо її аргументи мають неправильний (не [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) або [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)) тип даних.
```python
class MultiplyTest(unittest.TestCase):

...

def test_invalid_type_left(self):

    with self.assertRaises(TypeError) as err:
        x = multiply('aaa', 4)
    error_msg = 'Неприпустимий тип аргументу!'
    self.assertEqual(error_msg, err.exception.args[0])

```
Метод [```self.assertRaises()```](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertRaises) - повертає [контекстний менеджер](https://digitology.tech/posts/kontekstnye-menedzhery-v-python/) ```err```. Він зберігає перехоплений об'єкт виключення у своєму атрибуті ```err.exception```. Це може бути корисно, якщо ви збираєтеся додатково перевірити згенерований виняток. У нашому тесті перевіряється тип виключення [```TypeError```](https://docs.python.org/3/library/exceptions.html#TypeError) та текст повідомлення про помилку.

> Кожен тест слід забезпечити коментарем, в якому описаний тип тесту (позитивний або негативний), вхідні дані, очікувана поведінка (які дані будуть в результаті / яка помилка виникне, якщо тест негативний).  

Додамо коментарі до кожного тесту. Ось так тепер виглядає весь файл *multiply_test.py*:
```python
import unittest
from main import multiply


class MultiplyTest(unittest.TestCase):

    def test_invalid_type_left(self):
        """
        Негативний тест – неправильний тип даних лівого операнда. \n
        Буде викинуто помилку TypeError.
        """
        
        with self.assertRaises(TypeError) as err:
            x = multiply('aaa', 4)
        error_msg = 'Недопустимый тип аргумента!'
        self.assertEqual(error_msg, err.exception.args[0])
    
    def test_positive(self):
        """
        Позитивний тест – функція повертає добуток двох чисел.
        """
        
        a = 4
        b = 2
        result = multiply(a, b)
        self.assertEqual(a * b, result)
```

> Для **"Run with Coverage"** потрібен модуль *coverage.py*, якщо його немає у вашому проекті, IDE Pycharm запропонує встановити його.

Тепер можна запустити тест для нашої функції. Натисніть кнопку **"Run with Coverage"** у правій верхній частині екрана:

<p align=center><a href="https://imgbb.com/"><img src="https://i.ibb.co/TgP2yz2/image.png" alt="image" border="0"></a></p>

Якщо всі тести пройдуть успішно, ви побачите щось на кшталт цього:

<p align=center><a href="https://ibb.co/fCNZK5Z"><img src="https://i.ibb.co/8jxGRpG/test1.png" alt="test1" border="0"></a></br>
Які тести пройшли успішно (або не пройшли)</br></p>  
  
<p align=center><a href="https://imgbb.com/"><img src="https://i.ibb.co/Vm5M6zw/test2.png" alt="test2" border="0"></a></br>
Наскільки ваш код покритий тестами</p>  

Вимоги до тестів:
* Кожен тест повинен використовувати власний набір вхідних даних.
* Результат виконання одного тесту не повинен залежати від інших тестів.
* Тест повинен падати при неправильній поведінці тестованої функції
* Тести повинні покривати всі гілки умов у тілі функції та винятки, які вона може викинути.

## Корисна інформація
Видео:  
* [переказ цієї інструкції](https://www.youtube.com/watch?v=YD7aYJh3k-w), 
* [трохи теорії](https://www.youtube.com/watch?v=Rz4S0v7K7Ho)

Статті та документація:
* [Короткий посібник та рекомендації](https://ru.hexlet.io/courses/advanced_python/lessons/python_testing_unittest/theory_unit) (може знадобитися VPN)
* [Ще стаття](https://tirinox.ru/unit-test-python/)
* [unittest — Unit testing framework](https://docs.python.org/3/library/unittest.html) - документация Python unittest.

## Приклад – програма "Калькулятор" [(вихідний код)](https://github.com/Ceowyllian/Calculator)
Написати програму «Калькулятор», яка може виконувати 4 операції над числами a та b: 
* додавання ```+```
* віднімання ```-```
* добуток ```*```
* ділення ```/``` 

Результат кожної операції обчислює окрема функція. 

Для ```+```, ```-```, ```*``` результат буде цілим чи дійсним числом, залежно від типів аргументів. 

Операція ```/``` завжди повинна повертати дійсний результат. 

Що потрібно протестувати:
* Тип даних операндів – має бути [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) или [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex). Функція повинна генерувати виняток, якщо тип неправильний.
```python
def test_invalid_type_left(self):
    """
    Негативний тест – неприпустимий тип даних лівого операнда. \n
    Згенерує виняток TypeError.
    """

    with self.assertRaises(TypeError) as err:
        main.validate_type('aaa', 4)
    error_msg = 'Неприпустимий тип аргументу!'
    self.assertEqual(error_msg, err.exception.args[0])
```

* Тип результату, що повертається – повинен бути [```int```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) або [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) для ```+```, ```-```, ```*``` (залежно від типів операндів) і [```float```](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex) для ділення.
* Для операції розподілу правий операнд може бути нульовим. Перевірити, що функція розподілу викине [```ZeroDivisionError```](https://docs.python.org/3/library/exceptions.html#ZeroDivisionError) при спробі поділити на нуль.
```python
def test_division_by_zero(self):
    """
    Негативний тест - правий операнд дорівнює нулю. \n
    Згенерує виняток ZeroDivisionError.
    """

    a = 5
    b = 0
    with self.assertRaises(ZeroDivisionError) as err:
        main.divide(a, b)
    error_msg = 'На нуль ділити не можна!'
    self.assertEqual(error_msg, err.exception.args[0])
```

•	Позитивні тести – функції повинні виконувати ті операції, які описані у завданні. Порівняти результат, що повертається функцією та результат арифметичної операції.
```python
def test_both_args_are_float(self):
    """
    Позитивний тест - операнди типу float. \n
    Результат має тип float.
    """

    a = 4.6
    b = 3.1
    result = main.minus(a, b)
    self.assertIsInstance(result, float)
    self.assertEqual(a - b, result)
```
