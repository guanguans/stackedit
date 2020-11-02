\> 本文由 \[简悦 SimpRead\](http://ksria.com/simpread/) 转码， 原文地址 \[learnku.com\](https://learnku.com/articles/38719)[](javascript:void(0);)

*   explain 的用途

*   explain 的执行效果

*   explain 包含的字段

*   图片版

*   文字版

*   id 字段
*   select\_type 字段
*   type 字段
*   table 字段
*   possible\_keys 字段
*   key 字段
*   key\_len 字段
*   ref 字段
*   rows 字段
*   partitions 字段
*   filtered 字段
*   Extra 字段
*   使用的数据表

> explain 关键字可以模拟 MySQL 优化器执行 SQL 语句，可以很好的分析 SQL 语句或表结构的性能瓶颈。

explain 的用途[#](#53479c)
-----------------------

```
`1. 表的读取顺序如何
2. 数据读取操作有哪些操作类型
3. 哪些索引可以使用
4. 哪些索引被实际使用
5. 表之间是如何引用
6. 每张表有多少行被优化器查询
......`Copy
```

explain 的执行效果[#](#b70780)
-------------------------

```
`mysql> explain select * from subject where id = 1 \G
******************************************************
           id: 1
  select_type: SIMPLE
        table: subject
   partitions: NULL
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
******************************************************`Copy
```

explain 包含的字段[#](#ac58cc)
-------------------------

```
`1. id //select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序
2. select_type //查询类型
3. table //正在访问哪个表
4. partitions //匹配的分区
5. type //访问的类型
6. possible_keys //显示可能应用在这张表中的索引，一个或多个，但不一定实际使用到
7. key //实际使用到的索引，如果为NULL，则没有使用索引
8. key_len //表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度
9. ref //显示索引的哪一列被使用了，如果可能的话，是一个常数，哪些列或常量被用于查找索引列上的值
10. rows //根据表统计信息及索引选用情况，大致估算出找到所需的记录所需读取的行数
11. filtered //查询的表行占表的百分比
12. Extra //包含不适合在其它列中显示但十分重要的额外信息`Copy
```

图片版[#](#09c84b)
---------------

[![一张图搞定explain](https://cdn.learnku.com/uploads/images/201912/26/32495/rPhumO22r1.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/rPhumO22r1.png!large)

文字版[#](#ad2f92)
---------------

### id 字段[#](#b27234)

##### 1\. id 相同[#](#244ede)

```
`执行顺序从上至下
例子：
explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
读取顺序：subject > teacher > student_score`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/rjKbHBJIwk.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/rjKbHBJIwk.png!large)

##### 2\. id 不同[#](#f68e4e)

```
`如果是子查询，id的序号会递增，id的值越大优先级越高，越先被执行
例子：
explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
读取顺序：teacher > subject > student_score`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/8TYMhImxXL.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/8TYMhImxXL.png!large)

##### 3\. id 相同又不同[#](#2eba5d)

```
`id如果相同，可以认为是一组，从上往下顺序执行
在所有组中，id值越大，优先级越高，越先执行
例子：
explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
 -> union 
 -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
 读取顺序：2.teacher > 2.subject > 1.subject > 1.teacher`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/itY9FAcBNd.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/itY9FAcBNd.png!large)

### select\_type 字段[#](#18b12a)

##### 1\. SIMPLE[#](#4e10ce)

```
`简单查询，不包含子查询或Union查询
例子：
explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/Giey8y0X9u.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/Giey8y0X9u.png!large)

##### 2\. PRIMARY[#](#88e441)

```
`查询中若包含任何复杂的子部分，最外层查询则被标记为主查询
例子：
explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/32tGWp3WQC.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/32tGWp3WQC.png!large)

##### 3\. SUBQUERY[#](#e48b6e)

```
`在select或where中包含子查询
例子：
explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/UusOZ0W3WD.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/UusOZ0W3WD.png!large)

##### 4\. DERIVED[#](#da71cf)

```
`在FROM列表中包含的子查询被标记为DERIVED（衍生），MySQL
会递归执行这些子查询，把结果放在临时表中
备注：
MySQL5.7+ 进行优化了，增加了derived_merge（派生合并），默认开启，可加快查询效率`Copy
```

##### 5\. UNION[#](#ea11cd)

```
`若第二个select出现在uion之后，则被标记为UNION
例子：
explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
 -> union 
 -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/OhVLTznjVO.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/OhVLTznjVO.png!large)

##### 6\. UNION RESULT[#](#13870b)

```
`从UNION表获取结果的select
例子：
explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
 -> union 
 -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/Hqw1KvxxF9.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/Hqw1KvxxF9.png!large)

### type 字段[#](#290d88)

```
`NULL>system>const>eq_ref>ref>fulltext>ref_or_null>index_merge>unique_subquery>index_subquery>range>index>ALL //最好到最差
备注：掌握以下10种常见的即可
NULL>system>const>eq_ref>ref>ref_or_null>index_merge>range>index>ALL`Copy
```

##### 1\. NULL[#](#4e3e3c)

```
`MySQL能够在优化阶段分解查询语句，在执行阶段用不着再访问表或索引
例子：
explain select min(id) from subject;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/ujFB4tLGBW.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/ujFB4tLGBW.png!large)

##### 2\. system[#](#802357)

```
`表只有一行记录（等于系统表），这是const类型的特列，平时不大会出现，可以忽略`Copy
```

##### 3\. const[#](#1e943e)

```
`表示通过索引一次就找到了，const用于比较primary key或uique索引，因为只匹配一行数据，所以很快，如主键置于where列表中，MySQL就能将该查询转换为一个常量
例子：
explain select * from teacher where teacher_no = 'T2010001';`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/3MGg7McYaZ.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/3MGg7McYaZ.png!large)

##### 4\. eq\_ref[#](#783385)

```
`唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描
例子：
explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/bOYKg0bRR0.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/bOYKg0bRR0.png!large)

##### 5\. ref[#](#8b45ac)

```
`非唯一性索引扫描，返回匹配某个单独值的所有行
本质上也是一种索引访问，返回所有匹配某个单独值的行
然而可能会找到多个符合条件的行，应该属于查找和扫描的混合体
例子：
explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/zBpQvUWoEo.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/zBpQvUWoEo.png!large)

##### 6\. ref\_or\_null[#](#723203)

```
`类似ref，但是可以搜索值为NULL的行
例子：
explain select * from teacher where name = 'wangsi' or name is null;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/MPT1DJIgRc.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/MPT1DJIgRc.png!large)

##### 7\. index\_merge[#](#bee6ab)

```
`表示使用了索引合并的优化方法
例子：
explain select * from teacher where id = 1 or teacher_no = 'T2010001' .`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/oDgVdhYoTO.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/oDgVdhYoTO.png!large)

##### 8\. range[#](#5be7da)

```
`只检索给定范围的行，使用一个索引来选择行，key列显示使用了哪个索引
一般就是在你的where语句中出现between、<>、in等的查询。
例子：
explain select * from subject where id between 1 and 3;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/iAsTCHGXXW.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/iAsTCHGXXW.png!large)

##### 9\. index[#](#a88d46)

```
`Full index Scan，Index与All区别：index只遍历索引树，通常比All快
因为索引文件通常比数据文件小，也就是虽然all和index都是读全表，但index是从索引中读取的，而all是从硬盘读的。
例子：
explain select id from subject;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/upFuSQ39jb.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/upFuSQ39jb.png!large)

##### 10\. ALL[#](#be4475)

```
`Full Table Scan，将遍历全表以找到匹配行
例子：
explain select * from subject;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/r2UWJoVEuU.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/r2UWJoVEuU.png!large)

### table 字段[#](#85c6ea)

```
`数据来自哪张表`Copy
```

### possible\_keys 字段[#](#658b92)

```
`显示可能应用在这张表中的索引，一个或多个
查询涉及到的字段若存在索引，则该索引将被列出，但不一定被实际使用`Copy
```

### key 字段[#](#bfc7be)

```
 `实际使用到的索引，如果为NULL，则没有使用索引
查询中若使用了覆盖索引（查询的列刚好是索引），则该索引仅出现在key列表`Copy
```

### key\_len 字段[#](#a318c6)

```
 `表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度
在不损失精确度的情况下，长度越短越好
key_len显示的值为索引字段最大的可能长度，并非实际使用长度
即key_len是根据定义计算而得，不是通过表内检索出的`Copy
```

### ref 字段[#](#81049c)

```
 `显示索引的哪一列被使用了，如果可能的话，是一个常数，哪些列或常量被用于查找索引列上的值`Copy
```

### rows 字段[#](#a824ff)

```
 `根据表统计信息及索引选用情况，大致估算出找到所需的记录所需读取的行数`Copy
```

### partitions 字段[#](#a100e7)

```
 `匹配的分区`Copy
```

### filtered 字段[#](#39ddc9)

```
 `查询的表行占表的百分比`Copy
```

### Extra 字段[#](#2971ba)

```
 `包含不适合在其它列中显示但十分重要的额外信息`Copy
```

##### 1\. Using filesort[#](#70d5a9)

```
`说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取
MySQL中无法利用索引完成的排序操作称为“文件排序”
例子：
explain select * from subject order by name;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/pPkGsO8RWo.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/pPkGsO8RWo.png!large)

##### 2\. Using temporary[#](#409ec7)

```
`使用了临时表保存中间结果，MySQL在对结果排序时使用临时表，常见于排序order by 和分组查询group by
例子：
explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id
 -> union 
 -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/Piz1n8OfOF.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/Piz1n8OfOF.png!large)

##### 3\. Using index[#](#a37b06)

```
`表示相应的select操作中使用了覆盖索引（Covering Index）,避免访问了表的数据行，效率不错！
如果同时出现using where，表明索引被用来执行索引键值的查找
如果没有同时出现using where，表明索引用来读取数据而非执行查找动作
例子：
explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
备注：
覆盖索引：select的数据列只用从索引中就能够取得，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，即查询列要被所建的索引覆盖`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/HjO1TgE585.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/HjO1TgE585.png!large)

##### 4\. Using where[#](#b13970)

```
`使用了where条件
例子：
explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/3AWqqa0Pho.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/3AWqqa0Pho.png!large)

##### 5\. Using join buffer[#](#884cf5)

```
`使用了连接缓存
例子：
explain select student.*,teacher.*,subject.* from student,teacher,subject;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/R7HZqb6shJ.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/R7HZqb6shJ.png!large)

##### 6\. impossible where[#](#e8c3b5)

```
`where子句的值总是false，不能用来获取任何元组
例子：
explain select * from teacher where name = 'wangsi' and name = 'lisi';`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/BJNo3KlPxK.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/BJNo3KlPxK.png!large)

##### 7\. distinct[#](#5b27e2)

```
`一旦mysql找到了与行相联合匹配的行，就不再搜索了
例子：
explain select distinct teacher.name from teacher left join subject on teacher.id = subject.teacher_id;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/ZIyUN8DGAC.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/ZIyUN8DGAC.png!large)

##### 8\. Select tables optimized away[#](#b38ef5)

```
`SELECT操作已经优化到不能再优化了（MySQL根本没有遍历表或索引就返回数据了）
例子：
explain select min(id) from subject;`Copy
```

[![一张图搞定 explain](https://cdn.learnku.com/uploads/images/201912/26/32495/1MjAw28Uzl.png!large)](https://cdn.learnku.com/uploads/images/201912/26/32495/1MjAw28Uzl.png!large)

### 使用的数据表[#](#02e7b4)

```
`create table subject(
 -> id int(10) auto_increment,
 -> name varchar(20),
 -> teacher_id int(10),
 -> primary key (id),
 -> index idx_teacher_id (teacher_id));//学科表

create table teacher(
 -> id int(10) auto_increment,
 -> name varchar(20),
 -> teacher_no varchar(20),
 -> primary key (id),
 -> unique index unx_teacher_no (teacher_no(20)));//教师表

 create table student(
 -> id int(10) auto_increment,
 -> name varchar(20),
 -> student_no varchar(20),
 -> primary key (id),
 -> unique index unx_student_no (student_no(20)));//学生表

 create table student_score(
 -> id int(10) auto_increment,
 -> student_id int(10),
 -> subject_id int(10),
 -> score int(10),
 -> primary key (id),
 -> index idx_student_id (student_id),
 -> index idx_subject_id (subject_id));//学生成绩表

 alter table teacher add index idx_name(name(20));//教师表增加名字普通索引

 数据填充：
 insert into student(name,student_no) values ('zhangsan','20200001'),('lisi','20200002'),('yan','20200003'),('dede','20200004');

 insert into teacher(name,teacher_no) values('wangsi','T2010001'),('sunsi','T2010002'),('jiangsi','T2010003'),('zhousi','T2010004');

 insert into subject(name,teacher_id) values('math',1),('Chinese',2),('English',3),('history',4);

insert into student_score(student_id,subject_id,score) values(1,1,90),(1,2,60),(1,3,80),(1,4,100),(2,4,60),(2,3,50),(2,2,80),(2,1,90),(3,1,90),(3,4,100),(4,1,40),(4,2,80),(4,3,80),(4,5,100);`Copy
```

[mysql](https://learnku.com/blog/zhangdeTalk/tags/mysql_487) [explain](https://learnku.com/blog/zhangdeTalk/tags/explain_10624)

> 本作品采用[《CC 协议》](https://learnku.com/docs/guide/cc4.0/6589)，转载必须注明作者和本文链接

阿德