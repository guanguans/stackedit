> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://www.jianshu.com/p/aa47c2c4b104

MySQL 查询最近 10 天的数据，没有数据自动补 0; 正常的数据库中的表, 某一天如果没有数据的话, 以 ctime 字段为基准进行数据统计就会出现订单量为 0 的统计不到数据;

## 1、想方设法的创建一张虚拟的数据表, 专门表示日期.如下

``` SQL
SELECT
	tbl._date AS _date,
	IFNULL( tbr.totalnum, 0 ) AS totalnum 
FROM
	(
	SELECT
		@s := @s + 1 AS _index,
		DATE( DATE_SUB( CURRENT_DATE, INTERVAL @s DAY ) ) AS _date 
	FROM
		information_schema.TABLES,# mysql.help_topic,
# 关联任意一张系统表; 一定的是系统表;
		( SELECT @s := - 1 ) temp 
	WHERE
		@s < 10 
	ORDER BY
		_date 
	) AS tbl
	LEFT JOIN (
	SELECT
		count(*) AS totalnum,
		DATE( ctime ) createdate 
	FROM
		zxx_news 
	GROUP BY
		createdate 
	) AS tbr ON tbl._date = tbr.createdate 
GROUP BY
	tbl._date;
```

![](//upload-images.jianshu.io/upload_images/5189018-5447b20355e0c0c3.png?imageMogr2/auto-orient/strip|imageView2/2/w/969/format/webp)

## 2、在做报表数据统计的时候，用一个折线图用来显示并统计最近 6 个月内的数据结算情况。为了前端小伙伴方便处理数据，需要将没有数据的月份默认填充为 0

``` SQL

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NDM0MzQ2MjRdfQ==
-->