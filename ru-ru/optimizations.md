---
layout: default
language: 'ru-ru'
version: '0.12'
---

# Оптимизации

Because the code in Zephir is sometimes very high-level, a C compiler might not be able to optimize this code enough.

Zephir, thanks to its AOT (ahead-of-time) compiler, is able to optimize the code at compile time, potentially improving its execution time, or reducing the memory required by the program.

You can enable optimizations by passing the name prefixed by `-f`:

```bash
zephir -fstatic-type-inference -flocal-context-pass
```

Optimizations can be disabled by passing the name prefixed by `-fno-`:

```bash
zephir -fno-static-type-inference -fno-call-gatherer-pass
```

Оптимизации также могут настроены в конфигурационном файле `config.json`, как показано ниже:

```json
{
  "namespace": "mae",
  "name": "My Awesome Extension",
  "author": "ACME",
  "version": "1.0.0",

  "optimizations": {
    "static-type-inference": true,
    "static-type-inference-second-pass": true,
    "local-context-pass": true,
    "constant-folding": true,
    "static-constant-class-folding": true,
    "call-gatherer-pass": true,
    "check-invalid-reads": false,
    "private-internal-methods": false,
    "public-internal-methods": false,
    "public-internal-functions": true
  }
}
```

The following optimizations are supported:

<a id='call-gatherer-pass'></a>

## call-gatherer-pass

This pass counts how many times a function or method is called within the same method. This allows the compiler to introduce inline caches to avoid method or function lookups:

```zephir
class MyClass extends OtherClass
{

    public function getValue()
    {
        this->someMethod();

        // Этот метод может быть вызван быстрее
        this->someMethod();
    }
}
```

<a id='check-invalid-reads'></a>

## check-invalid-reads

This flag will force checking types to detect for invalid reads during the compilation process. This ensures that all variables are properly defined and initialized with their default values (as well as the internal pointers). An example is:

```zephir
namespace Acme;

class ForInRange
{
    public static function forEmpty(var n)
    {
        var i;
        for i in range(1, n) {
            // Здесь происходит полезная работа
        }
    }
}
```

сравнивается с:

```zephir
namespace Acme;

class ForInRange
{
    public static function forEmpty(var n)
    {
        var i = null;
        for i in range(1, n) {
            // Здесь происходит полезная работа
        }
    }
}
```

Both examples are perfectly valid as far as Zephir is concerned. The difference is in the generated C code:

```c
zval *n;

// ...

zephir_fetch_params(1, 1, 0, &n);
```

сравнивается с:

```c
zval *n = NULL;

// ...

zephir_fetch_params(1, 1, 0, &n);
```

It is a good practice to always initialize variables with default values and types for any programming language. Not doing so, could potentially have unintended consequences for the application, and introduce bugs, memory leaks etc. By using the `check-invalid-reads` flag in `config.json` we ensure that pointers are properly initialized along with their respective C variables. Zephir-разработчики не увидят изменений в коде. This affects the generated C code.

More information concerning on why C pointers need to be nullified in Stack overflow [here](https://stackoverflow.com/q/12253191/1661465).

<a id='constant-folding'></a>

## constant-folding

Constant folding is the process of simplifying constant expressions at compile time. The following code is simplified when this optimization is enabled:

```zephir
public function getValue()
{
    return (86400 * 30) / 12;
}
```

Преобразуется в:

```zephir
public function getValue()
{
    return 216000;
}
```

<a id='internal-call-transformation'></a>

## internal-call-transformation

The `internal-call-transformation` is required to generate internal methods, based on their equivalent PHP ones, allowing for the bypass of the PHP userspace for those internal method calls. По умолчанию, эта оптимизация отключена.

This optimization generates 2 implementations per method, one that is exposed in PHP and an internal one.

Exceptions to the above are:

- Only PHP methods allowed to be replaced (eg. мы не можем сделать это для методов Phalcon)
- Замыкания (`__invoke`) и `__construct` не поддерживаются
- Количество требуемых параметров должно точно соответствовать количеству реальных параметров
- Не работает для ZendEngine2 (PHP 5.6)

<a id='local-context-pass'></a>

## local-context-pass

Этот этап компиляции перемещает переменные, которые размещены в куче, в стек. Эта оптимизация может уменьшить количество косвенных обращений к памяти, которые должна выполнять программа.

<a id='static-constant-class-folding'></a>

## static-constant-class-folding

Эта оптимизация заменяет значения констант класса во время компиляции:

```zephir
class MyClass
{

    const SOME_CONSTANT = 100;

    public function getValue()
    {
        return self::SOME_CONSTANT;
    }
}
```

Преобразуется в:

```zephir
class MyClass
{

    const SOME_CONSTANT = 100;

    public function getValue()
    {
        return 100;
    }
}
```

<a id='static-type-inference'></a>

## static-type-inference

Этот этап компиляции очень важен, поскольку он ищет динамические переменные, которые потенциально могут быть преобразованы в статические/примитивные типы, которые лучше оптимизируются базовым компилятором.

Следующий код использует набор динамических переменных для выполнения некоторых математических вычислений:

```zephir
public function someCalculations(var a, var b)
{
    var i = 0, t = 1;

    while i < 100 {
        if i % 3 == 0 {
            continue;
        }
        let t += (a - i), i++;
    }

    return i + b;
}
```

Переменные `a`, `b` и `i` используются исключительно для математических операций, и таким образом их можно преобразовать в статические переменные, используя преимущества других этапов компиляции. После выполнения компилятор автоматически переписывает этот код в:

```zephir
public function someCalculations(int a, int b)
{
    int i = 0, t = 1;

    while i < 100 {
        if i % 3 == 0 {
            continue;
        }
        let t += (a - i), i++;
    }

    return i + b;
}
```

Отключив этот этап компиляции, все переменные будут поддерживать тип, с которым они были первоначально объявлены, без оптимизации.

<a id='static-type-inference-second-pass'></a>

## static-type-inference-second-pass

This enables a second type inference pass, which improves the work done based on the data gathered by the first static type inference pass.