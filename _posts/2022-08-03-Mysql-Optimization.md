---
layout: post
title: Mysql的优化
category: MySQL
tags: [MySQL]
---

## 简介



**参考文档**

[官网第八章](https://dev.mysql.com/doc/refman/5.7/en/generated-column-index-optimizations.html)



为了优化MySQL的表现

主要可以从如下几个方向进行

* 数据库等级的优化

  * 表格是否是合适构造

    > 如果更新很多的数据库需要多个表
    >
    > 每个有很少的列
    >
    > 如果需要分析大量数据，需要少的表多个列

  * 是否有合适的**索引**

  * 是否选择了合适的**存储引擎**（*InnoDB or MySIAM*），以及利用了**合适的格式**

    ...

* 硬件层面的优化

  * *硬盘的搜寻*：优化搜索时间
  * *硬盘读取和书写*：速度可以提升
  * CPU
  * Memory宽度：主要内存的带宽

* 平衡便携和表现

  





## 优化SQL表达式





### 优化**Select**语句



#### Where语句优化



1. 看看是否可以增添**Index**

   where语句所需要的列需要索引

   > 可以用EXPLAIN语句去判断SELECT使用了那个索引

2. 减少序列中的**全表扫描**，对于大型的表

3. 语句中减少**不必要的括号**

4. 多用**常数**，而不是*变量*

   ~~~sql
      (a<b AND b=c) AND a=5
   -> b>5 AND b=c AND a=5
   ~~~



#### Range语句的优化



也是用常数替换变量是最好的选择





## 优化索引（Index）



### 索引的基本介绍



**目的**

索引是用来<u>对于具体的列找到需要的行</u>

如果没有索引，只能从头开始一个一个找。



**索引类型**

包括**Primary Key，Unique，Index，Fulltext**都是存储为**B树**

但是

* spatial data使用R tree
* Memory 表格使用hash index
* InnoDB 对于list使用 Fulltext







### Primary Key优化



如果没有主键的列

可以用自己添加的auto_increment作为主键









### Foreign Key 优化



外码的出现在于<u>我们的原始表格很大，要把数据划分到不同的表格之中</u>

所以要把其变成多个**表格**，然后用**外键**连接

这样可以减少I/O操作，并使用更少的缓存记忆





### Column 索引











**Index Prefixes**

对于文本，可以指定索引长度

只会根据指定长度建立索引

~~~
CREATE TABLE test (blob_col BLOB, INDEX(blob_col(10)));
~~~







**FullText Index**





**Spatial Index**







### 多列索引

~~~sql
CREATE TABLE test (
    id         INT NOT NULL,
    last_name  CHAR(30) NOT NULL,
    first_name CHAR(30) NOT NULL,
    PRIMARY KEY (id),
    INDEX name (last_name,first_name)
);
~~~











### 比较B-Tree和Hash索引





**B树的性质**

可以更好用于**比较运算符**



**Hash索引性质**

对于**等于号**查找非常迅速

但是不适应其他的



不能加速order by





