---
layout: default
language: 'zh-cn'
version: '0.11'
---

# phpinfo () 部分
Like most extensions, Zephir extensions are able to show information in the [phpinfo()](https://php.net/manual/en/function.phpinfo.php) output. 这些信息通常与指令、环境数据等有关。

默认情况下, 每个 Zephir扩展都会自动向显示扩展版本和扩展所支持的任何 ini 选项的 `phpinfo()` 输出中添加一个基本表。

通过向 `config.json` 文件中添加以下配置, 可以添加更多指令:

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

此信息将如下所示:

![](/assets/content/info.png)
