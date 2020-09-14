\> 本文由 \[简悦 SimpRead\](http://ksria.com/simpread/) 转码， 原文地址 \[www.jianshu.com\](https://www.jianshu.com/p/f9459e609130) ![](//upload-images.jianshu.io/upload_images/3827973-b738eff89cd24a7a.png?imageMogr2/auto-orient/strip|imageView2/2/w/858/format/webp) usage.png

> laravel 中自带的查询构建方法 `toSql` 得到的 sql 语句并未绑定条件参数，类似于这样 `select * from`users`where`id`= ?`，所以写了个扩展包 [laravel-dump-sql](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fguanguans%2Flaravel-dump-sql) ，可以获取完整的 sql 语句。

源码
--

*   [laravel-dump-sql](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fguanguans%2Flaravel-dump-sql) - [https://github.com/guanguans/laravel-dump-sql](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fguanguans%2Flaravel-dump-sql)

安装
--

```
$ composer require guanguans/laravel-dump-sql -v 
```

### 发布服务

```
$ php artisan vendor:publish --provider="Guanguans\\LaravelDumpSql\\ServiceProvider" 
```

使用
--

> 安装成功后查询构建器会新增 `toRawSql`、`dumpSql`、`ddSql` 三个方法

```
// 获取 SQL
User::where('id', 1)->toRawSql();
DB::table('user')->where('id', 1)->toRawSql();

// 打印 SQL
User::where('id', 1)->dumpSql();
DB::table('user')->where('id', 1)->dumpSql();

// 打印 SQL 并退出
User::where('id', 1)->ddSql();
DB::table('user')->where('id', 1)->ddSql(); 
```

自定义方法名称
-------

### 发布配置文件

```
$ php artisan vendor:publish --tag=laravel-dump-sql 
```

### `config/dumpsql.php` 文件中配置方法名称既可

```
<?php
return [
    /*
     * Get sql statement.
     */
    'to_raw_sql' => 'toRawSql',

    /*
     * Print SQL statements.
     */
    'dump_sql' => 'dumpSql',

    /*
     * Print SQL statements and exit.
     */
    'dd_sql' => 'ddSql',
]; 
```