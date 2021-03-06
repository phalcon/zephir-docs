---
layout: default
language: 'ru-ru'
version: '0.12'
---

# Глобальные параметры расширения

PHP расширения предоставляют способ определения глобальных переменных, которые будут доступны из любого места расширения. Операции чтения и записи таких переменных обычно выполняются быстрее, чем любые другие глобальные механизмы (например, доступ к статическим членам). Глобальные переменные расширения можно использовать для настройки параметров конфигурации, которые изменяют поведение вашей библиотеки.

В Zephir глобальные переменные ограничены простыми скалярными типами такими как `int`, `bool`, `double`, `char` и т.д. Здесь не допускаются комплексные типы, такие как строки, массивы, объекты и ресурсы.

Вы можете ввести глобальные переменные в расширение, добавив следующую структуру в `config.json`:

```json
{
    "globals": {
        "allow_some_feature": {
            "type": "bool",
            "default": true,
            "module": true
        },
        "number_times": {
            "type": "int",
            "default": 10
        },
        "some_component.my_setting_1": {
            "type": "bool",
            "default": true
        },
        "some_component.my_setting_2": {
            "type": "int",
            "default": 100
        }
    }
}
```

Каждая глобальная переменная имеет следующую структуру:

```json
"<global-name>": {
    "type": "<some-valid-type>",
    "default": <some-compatible-default-value>
}
```

Составные (именованные) глобальные переменные имеют следующую структуру:

```json
"<namespace>.<global-name>": {
    "type": "<some-valid-type>",
    "default": <some-compatible-default-value>
}
```

Необязательный ключ `module`, если он присутствует, помещает процесс инициализации параметра в глобальное событие расширения `GINIT`. Это означает, что параметр будет инициализирован указанным значением только один раз для каждого процесса PHP, а не будет повторно инициализирован для каждого запроса, что является значением по умолчанию:

```json
{
    "globals": {
        "allow_some_feature": {
            "type": "bool",
            "default": true,
            "module": true
        },
        "number_times": {
            "type": "int",
            "default": 10
        }
    }
}
```

В примере выше, `allow_some_feature` настраивается только один раз при запуске; `number_times` устанавливается в начале каждого запроса.

Доступ к глобальным переменным может быть осуществлён из любого метода расширения при помощи встроенных функций `globals_get` и `globals_set`:

```zephir
globals_set("allow_some_feature", true);
let someFeature = globals_get("allow_some_feature");
```

Если вы хотите получать доступ к этим глобальным переменным из PHP, то неплохим вариантом является создание метода, направленного на это:

```zephir
namespace Test;

class MyOptions
{

    public static function setOptions(array options)
    {
        boolean someOption, anotherOption;

        if fetch someOption, options["some_option"] {
            globals_set("some_option", someOption);
        }

        if fetch anotherOption, options["another_option"] {
            globals_set("another_option", anotherOption);
        }
    }
}
```

Доступ к глобальным переменным расширения не может быть осуществлен с помощью динамически вычисляемого кода. Это связанно с тем, что C-код, сгенерированный оптимизаторами функций `globals_get` и `globals_set`, должен быть вычислен во время компиляции:

```zephir
let myOption = "someOption";

// Выбросит ошибку компиляции
let someOption = globals_get(myOption);
```