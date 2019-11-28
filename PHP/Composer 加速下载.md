# Composer 加速下载

<img width="1122" alt="" src="https://user-images.githubusercontent.com/22309277/58367647-6c074500-7f14-11e9-9a19-65346ac447f9.png">

## 配置国内镜像源

### Composer 中文网镜像

``` shell
$ composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

### laravel-china 镜像

``` shell
$ composer config -g repo.packagist composer https://packagist.laravel-china.org
```

## 开启多线程下载

[`hirak/prestissimo`](https://github.com/hirak/prestissimo) 这是一个可以为 Composer 开启多线程的下载的包，安装以后会自动生效。

``` shell
$ composer global require hirak/prestissimo
```

## Composer 镜像管理工具

* [slince/composer-registry-manager](https://laravel-china.org/topics/4134/amway-composer-source-management-tool-slincecrm)

<!--stackedit_data:
eyJoaXN0b3J5IjpbOTE1NDAyNDQsOTE1NDAyNDRdfQ==
-->