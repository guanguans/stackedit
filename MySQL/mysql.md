mysql查询最近10天的数据，没有数据自动补0; 正常的数据库中的表, 某一天如果没有数据的话, 以ctime字段为基准进行数据统计就会出现订单量为0的统计不到数据;

原网址：[https://blog.csdn.net/sq_com/article/details/84997563](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fsq_com%2Farticle%2Fdetails%2F84997563)

  

解决方法:

  

1. 想方设法的创建一张虚拟的数据表, 专门表示日期.如下:

  

```

SELECT

tbl._date AS _date,

IFNULL(tbr.totalnum, 0) AS totalnum

FROM

(

SELECT

@s :=@s + 1 AS _index,

DATE(

DATE_SUB(CURRENT_DATE, INTERVAL @s DAY)

) AS _date

FROM

information_schema.tables,

# mysql.help_topic,

# 关联任意一张系统表; 一定的是系统表;

(SELECT @s := - 1) temp

WHERE

@s < 10

ORDER BY

_date

) AS tbl

LEFT JOIN (

SELECT count(*) AS totalnum, DATE(ctime) createdate

FROM zxx_news GROUP BY createdate ) AS tbr ON tbl._date = tbr.createdate

GROUP BY tbl._date ;

```

  

![](//upload-images.jianshu.io/upload_images/5189018-5447b20355e0c0c3.png?imageMogr2/auto-orient/strip|imageView2/2/w/969/format/webp) 图片.png

  

2.在做报表数据统计的时候，用一个折线图用来显示并统计最近 6 个月内的数据结算情况。为了前端小伙伴方便处理数据，需要将没有数据的月份默认填充为 0

  

```

SELECT

CONVERT (t2.year_month_str, CHAR) `timeStamp`,

IFNULL(SUM(i.id), 0) AS total

FROM

(

SELECT

@rownum :=@rownum + 1 AS num,

date_format(

DATE_SUB(now(), INTERVAL @rownum MONTH),

'%Y/%m'

) AS year_month_str

FROM

(SELECT @rownum := - 1) AS r_init,

(

SELECT

c.id

FROM

zxx_news c

LIMIT 6

) AS c_init

) t2

LEFT JOIN zxx_news AS i ON (

CONCAT(

DATE_FORMAT(i.ctime, '%Y'),

'/',

DATE_FORMAT(i.ctime, '%m')

) = t2.year_month_str

AND i.id > 100

)

GROUP BY

t2.year_month_str

```

  

![](//upload-images.jianshu.io/upload_images/5189018-ca5468ea73e6d1dd.png?imageMogr2/auto-orient/strip|imageView2/2/w/1008/format/webp) 图片.png
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzY2NTA4MDMyXX0=
-->