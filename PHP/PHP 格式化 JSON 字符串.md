> [json-format]([https://github.com/guanguans/json-format](https://github.com/guanguans/json-format))
[https://github.com/guanguans/json-format](https://github.com/guanguans/json-format)
<p  align="center"><img src="https://raw.githubusercontent.com/guanguans/json-format/master/docs/usage.png"></p>

## 环境要求

* PHP > 5.4
* ext-json

## 安装

``` shell
$ composer require guanguans/json-format -v
```

## 使用

### 代码

``` php
<?php

require  __DIR__.'/vendor/autoload.php';

$json =  <<<'JSON'
{"name":"json-format","keywords":["json","format","pretty"],"description":"Format json string."}
JSON;
echo  json_format($json);
```

### 输出

``` json
{
"name": "json-format",
"keywords": [
	"json",
	"format",
	"pretty"
],
"description": "Format json string."
}
```

## 测试

``` shell
$ composer test
```

## License

MIT
<!--stackedit_data:
eyJoaXN0b3J5IjpbODgwODAyMDk4LDY0MzQyODEwXX0=
-->