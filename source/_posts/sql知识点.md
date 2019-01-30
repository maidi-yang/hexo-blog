---
title: sql知识点
date: 2018-12-25 18:04:39
tags: [mysql]
categories: [技术,mysql]
---
# 数据库优化
1.从结构层: web服务器采用负载均衡服务器,mysql服务器采用主从复制,读写分离
2.从储存层: 采用合适的存储引擎,采用三范式
3.从设计层: 采用分区分表,索引,表的字段采用合适的字段属性,适当的采用逆范式,开启mysql缓存
4.sql语句层:结果一样的情况下,采用效率高,速度快节省资源的sql语句执行

# 三范式
## 第一范式
   1、每一列属性都是不可再分的属性值，确保每一列的原子性
   2、两列的属性相近或相似或一样，尽量合并属性一样的列，确保不产生冗余数据。

## 第二范式
每一行的数据只能与其中一列相关，即一行数据只做一件事。只要数据列中出现数据重复，就要把表拆分开来。

## 第三范式
 数据不能存在传递关系，即没个属性都跟主键有直接关系而不是间接关系。

# 索引
MySQL索引的建立对于MySQL的高效运行是很重要的，索引可以大大提高MySQL的检索速度。
打个比方，如果合理的设计且使用索引的MySQL是一辆兰博基尼的话，那么没有设计和使用索引的MySQL就是一个人力三轮车。
索引分单列索引和组合索引。单列索引，即一个索引只包含单个列，一个表可以有多个单列索引，但这不是组合索引。组合索引，即一个索引包含多个列。
创建索引时，你需要确保该索引是应用在 SQL 查询语句的条件(一般作为 WHERE 子句的条件)。
实际上，索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录。
上面都在说使用索引的好处，但过多的使用索引将会造成滥用。因此索引也会有它的缺点：虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。
建立索引会占用磁盘空间的索引文件。
--------------------------------------------------------------------------------
## 普通索引
### 创建索引
这是最基本的索引，它没有任何限制。它有以下几种创建方式：

`CREATE INDEX indexName ON mytable(username(length))`

如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length。

修改表结构(添加索引)

`ALTER table tableName ADD INDEX indexName(columnName)`

创建表的时候直接指定
```
CREATE TABLE mytable(  
 
ID INT NOT NULL,   
 
username VARCHAR(16) NOT NULL,  
 
INDEX [indexName] (username(length))  
 
);  
```
删除索引的语法

`DROP INDEX [indexName] ON mytable; `

--------------------------------------------------------------------------------
### 唯一索引
它与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。它有以下几种创建方式：
创建索引

`CREATE UNIQUE INDEX indexName ON mytable(username(length)) `
修改表结构

`LTER table mytable ADD UNIQUE [indexName] (username(length))`

创建表的时候直接指定
```
CREATE TABLE mytable(  
 
ID INT NOT NULL,   
 
username VARCHAR(16) NOT NULL,  
 
UNIQUE [indexName] (username(length))  
 
);  
```
--------------------------------------------------------------------------------
#### 使用ALTER 命令添加和删除索引

有四种方式来添加数据表的索引：
`ALTER TABLE tbl_name ADD PRIMARY KEY (column_list)`: 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。

`ALTER TABLE tbl_name ADD UNIQUE index_name (column_list)`: 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。

`ALTER TABLE tbl_name ADD INDEX index_name (column_list)`: 添加普通索引，索引值可出现多次。

`ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list)`:该语句指定了索引为 FULLTEXT ，用于全文索引。

以下实例为在表中添加索引。

`mysql> ALTER TABLE testalter_tbl ADD INDEX (c)`

你还可以在 ALTER 命令中使用 DROP 子句来删除索引。尝试以下实例删除索引:

`mysql> ALTER TABLE testalter_tbl DROP INDEX c`

--------------------------------------------------------------------------------

#### 使用 ALTER 命令添加和删除主键
主键只能作用于一个列上，添加主键索引时，你需要确保该主键默认不为空（NOT NULL）。实例如下：

`mysql> ALTER TABLE testalter_tbl MODIFY i INT NOT NULL;`

`mysql> ALTER TABLE testalter_tbl ADD PRIMARY KEY (i);`

你也可以使用 ALTER 命令删除主键：

`mysql> ALTER TABLE testalter_tbl DROP PRIMARY KEY;`

删除主键时只需指定PRIMARY KEY，但在删除索引时，你必须知道索引名。

## 索引的优缺点
1、优点：

a）可以保证数据库表中每一行的数据的唯一性 

b）可以大大加快数据的索引速度
 
c）加速表与表之间的连接，物别是在实现数据的参考完事性方面特别有意义 

d）在使用分组和排序子句进行数据检索时，同样可以显著减少查询中分组和排序的时间 

f）通过使用索引，可以在时间查询的过程中，使用优化隐藏器，提高系统的性能

2、 缺点： 
a) 创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加 

b) 索引需要占物理空间，除了数据表占用数据空间之外，每一个索引还要占用一定的物理空间，如果需要建立聚簇索引，那么需要占用的空间会更大 

c) 以表中的数据进行增、删、改的时候，索引也要动态的维护，这就降低了整数的维护速度
 
d) 建立索引的原则 

e) 在经常需要搜索的列上，可以加快搜索的速度 

f) 在作为主键的列上，强制该列的唯一性和组织表中数据的排列结构 

g) 在经常用在连接的列上，这些列主要是一外键，可以加快连接的速度 

h) 在经经常需要根据范围进行搜索的列上创建索引，国为索引已经排序，其指定的范围是连续的 

i) 在经常需要排序的列上，国为索引已经排序，这样井底可以利用索引的排序，加快排序井底时间 

j) 在经常使用在where子句中的列上，加快条件的判断速度