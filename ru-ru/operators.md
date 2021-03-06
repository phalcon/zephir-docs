---
layout: default
language: 'ru-ru'
version: '0.12'
---

# Операторы

This article describes Zephir's operators. For precedence, you can check the [Operator Precedence](/{{ page.version }}/{{ page.language }}/operator-precedence) article.

Zephir's operators are similar to the ones in PHP, and inherit some of their behaviors.

<a id='arithmetic-operators'></a>

## Арифметические операторы

Поддерживаются следующие операторы:

| Операция                    | Пример  |
| --------------------------- | ------- |
| Приведение к отрицательному | `-a`    |
| Сложение                    | `a + b` |
| Вычитание                   | `a - b` |
| Умножение                   | `a * b` |
| Деление                     | `a / b` |
| Деление по модулю           | `a % b` |

<a id='bitwise-operators'></a>

## Побитовые операторы

Поддерживаются следующие операторы:

| Операция        | Пример         |
| --------------- | -------------- |
| И               | `a & b`    |
| Или             | `a | b`        |
| Исключающее или | `a ^ b`        |
| Отрицание       | `~a`           |
| Сдвиг влево     | `a << b` |
| Сдвиг вправо    | `a >> b` |


Пример:

```zephir
if a & SOME_FLAG {
    echo "Флаг установлен";
}
```

Узнать больше о сравнении динамических переменных можно из [документации по PHP](https://www.php.net/manual/en/language.operators.comparison.php).

<a id='comparison-operators'></a>

## Операторы сравнения

Операции сравнения зависят от типа сравниваемых переменных. Например если оба операнда динамические, то результат будет таким же как и в PHP:

| Пример         | Операция             | Описание                                         |
| -------------- | -------------------- | ------------------------------------------------ |
| `a == b`       | Равенство            | `true` если a равно b после приведения типов.    |
| `a === b`      | Идентичность         | `true` если a равно b, и операнды одного типа.   |
| `a != b`       | Не равны             | `true` если a не равно b после приведения типов. |
| `a <> b` | Не равны             | `true` если a не равно b после приведения типов. |
| `a !== b`      | Не идентичны         | `true` если a не равно b после приведения типов. |
| `a < b`     | Меньше чем           | `true` если a строго меньше b.                   |
| `a > b`     | Больше чем           | `true` если a строго больше b.                   |
| `a <= b`    | Меньше чем или равно | `true` если a меньше, или равно b.               |
| `a >= b`    | Больше чем или равно | `true` если a строго больше, или равно b.        |


Пример:

```zephir
if a == b {
    return 0;
} else {
    if a < b {
        return -1;
    } else {
        return 1;
    }
}
```

<a id='logical-operators'></a>

## Логические операторы

Поддерживаются следующие операторы:

| Операция  | Пример           |
| --------- | ---------------- |
| И         | `a && b` |
| Или       | `a || b`         |
| Отрицание | `!a`             |


Пример:

```zephir
if a && b || !c {
    return -1;
}
return 1;
```

<a id='tenary-operator'></a>

## Тернарный оператор

Zephir поддерживает тернарный оператор, как в C или PHP:

```zephir
let b = a == 1 ? "x" : "y"; // b будет присвоен "x", если a равно 1 в противном случае "y"
```

<a id='special-operators'></a>

## Специальные операторы

Поддерживаются следующие операторы:

<a id='special-operators-empty'></a>

### Empty

Этот оператор позволяет узнать пусто ли выражение. Под "пусто" подразумевается выражение равное `null`, пустая строка или пустой массив:

```zephir
let someVar = "";
if empty someVar {
    echo "пусто!";
}

let someVar = "привет";
if !empty someVar {
    echo "не пусто!";
}
```

<a id='special-operators-fetch'></a>

### Fetch

Оператор "fetch" создан для сокращения популярной в PHP конструкции:

```php
<?php

if (isset($myArray[$key])) {
    $value = $myArray[$key];
    echo $value;
}
```

В Zephir тот же код будет можно написать так:

```zephir
if fetch value, myArray[key] {
    echo value;
}
```

Оператор "fetch" вернет `true`, если в массиве есть что-то по ключу "key" и тогда переменной "value" будет присвоено значение.

<a id='special-operators-isset'></a>

### Isset

Проверяет, существует ли индекс у массива или свойство у объекта:

```zephir
let someArray = ["a": 1, "b": 2, "c": 3];
if isset someArray["b"] { // проверим, есть ли у массива индекс "b"
    echo "Да, переменная имеет индекс 'b'\n";
}
```

Использование `isset` возможно и в возвращаемых конструкциях:

```zephir
return isset this->{someProperty};
```

Обратите внимание, `isset` в Zephir работает скорее как [array_key_exists](https://www.php.net/manual/en/function.array-key-exists.php) в PHP. Иными словами `isset` в Zephir вернёт true, даже если значение равно null.

<a id='special-operators-typeof'></a>

### Typeof

Этот оператор проверяет тип переменной. 'typeof' может использоваться с оператором сравнения:

```zephir
if (typeof str == "string") { // or !=
    echo str;
}
```

Он также может работать как PHP-функция `gettype`.

```zephir
return typeof str;
```

**Be careful**, if you want to check whether an object is 'callable', you always have to use `typeof` as a comparison operator, not a function.

<a id='special-operators-type-hints'></a>

### Подсказки типа

Zephir всегда пытается проверить, реализует ли объект методы и свойства, вызываемые/доступные для переменной, которая выводится как объект:

```zephir
let o = new MyObject();

// Zephir проверяет, реализован ли "myMethod" в MyObject
o->myMethod();
```

Однако из-за динамичной природы, унаследованной от PHP, иногда нелегко узнать класс объекта, поэтому Zephir не может эффективно создавать отчеты об ошибках. Подсказка типа сообщает компилятору, какой класс связан с динамической переменной, позволяющей компилятору выполнять больше проверок компиляции:

```zephir
// Сообщает компилятору, что "o"
// является экземпляром класса MyClass
let o = <MyClass> this->_myObject;
o->myMethod();
```

Эти «подсказки типов» являются слабыми. Это означает, что программа не проверяет, является ли значение экземпляром указанного класса, и не проверяет реализует ли он указанный интерфейс. Если вы хотите, чтобы это проверялось каждый раз при выполнении, используйте строгую типизацию:

```zephir
// Always check if the property is an instance
// of MyClass before the assignment
let o = <MyClass!> this->_myObject;
o->myMethod();
```

<a id='special-operators-branch-prediction-hints'></a>

### Подсказки прогнозирования ветвлений

Что такое прогнозирование ветвлений? Для подробного описания это понятия обратитесь к [статье Игоря Островского](https://igoro.com/archive/fast-and-slow-if-statements-branch-prediction-in-modern-processors/) или [описанию на Wikipedia](https://en.wikipedia.org/wiki/Branch_predictor). В окружениях, где производительность является очень важной составляющей, может оказаться полезным использование подсказок при прогнозировании ветвлений.

Рассмотрим следующий пример:

```zephir
let allPaths = [];
for path in this->_paths {
    if path->isAllowed() == false {
        throw new App\Exception("Некое сообщение об ошибке");
    } else {
        let allPaths[] = path;
    }
}
```

Авторы кода, приведенного выше, заранее знают, что условие, которое выбрасывает исключение, вряд ли произойдет. Это означает, что в 99.9% случаев наш метод выполняет эту проверку в холостую — условие вероятно не будет оцениваться как истинное. Но для процессора, обычно, это сложно понять, поэтому мы могли бы ввести здесь подсказку:

```zephir
let allPaths = [];
for path in this->_paths {
    if unlikely path->isAllowed() == false {
        throw new App\Exception("Некое сообщение об ошибке");
    } else {
        let allPaths[] = path;
    }
}
```