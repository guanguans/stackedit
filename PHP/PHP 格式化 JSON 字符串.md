<h1  align="center">json-format</h1>

<p  align="center">Format json string.</p>

<p  align="center"><img src="./docs/usage.png"></p>

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

  

### Output

  

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

  

## Testing

  

``` shell

$ composer test

```

  

## License

  

[MIT](LICENSE)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk5MjAwMjYzMiw2NDM0MjgxMF19
-->