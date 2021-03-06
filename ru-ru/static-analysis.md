---
layout: default
language: 'ru-ru'
version: '0.12'
---

# Статический анализ

Компилятор Zephir предоставляет возможность статического анализа компилируемого кода. Идея этой функции заключается в том, чтобы помочь разработчику найти потенциальные проблемы и избежать неожиданного поведения задолго до времени исполнения.

<a id='conditional-unassigned-variables'></a>

## Условные неинициализированные переменные

При присвоении значения переменно статический анализатор пытается определить, используется ли переменная до ее инициализации:

```zephir
class Utils
{
    public function someMethod(b)
    {
        string a; char c;

        if b == 10 {
            let a = "привет";
        }

        // Есть вероятность, что переменная "a" не определена
        for c in a {
            echo c, PHP_EOL;
        }
    }
}
```

Приведенный выше пример иллюстрирует общую ситуацию. Переменной `a` будет присвоено значение только в том случае, если переменная `b` равна 10, но как видно из этого примера, значение проверяемой переменной не определено в явном виде. Компилятор Zephir определяет это, автоматически инициализирует переменную в пустую строку и генерирует предупреждение разработчику:

```bash
Warning: Variable 'a' was assigned for the first time in conditional branch,
consider initialize it in its declaration in
/home/scott/test/test/utils.zep on 21 [conditional-initialization]

    for c in a {
```

Обнаружить такие ошибки иногда сложно, однако статический анализ помогает программисту обнаружить ошибки заранее.

<a id='dead-code-elimination'></a>

## Удаление мёртвого кода

Zephir информирует разработчика о недоступных ветвях в коде и выполняет удаление мертвого кода, это означает, что он избавляется от всего этого кода из сгенерированного двоичного файла, поскольку он никогда не сможет быть запущен:

```zephir
class Utils
{
    public function someMethod(b)
    {
        if false {
            // Этот код никогда не выполнится
            echo "hello";
        }
    }
}
```