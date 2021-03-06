---
layout: default
language: 'uk-ua'
version: '0.12'
---

# Розділи в phpinfo()

Як і більшість PHP-розширень, розширення написані на Zephir здатні показати інформацію у виводі [phpinfo()](https://php.net/manual/en/function.phpinfo.php). Зазвичай ця інформація відноситься до директив, даних оточення і т.д.

Типово, кожне Zephir-розширення додає базову таблицю у вивід `phpinfo()`, яка показує версію розширення.

Ви можете додати більше директив, додавши наступну конфігурацію в файл `config.json`:

```json
"info": [
    {
        "header": ["Directive", "Value"],
        "rows": [
            ["setting1", "value1"],
            ["setting2", "value2"]
        ]
    },
    {
        "header": ["Directive", "Value"],
        "rows": [
            ["setting3", "value3"],
            ["setting4", "value4"]
        ]
    }
]
```

Ця інформація буде показана наступним чином:

![](/assets/content/info.png)