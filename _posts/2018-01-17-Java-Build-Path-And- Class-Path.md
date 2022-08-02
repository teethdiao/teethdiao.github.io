---
layout: post
title: Build Path and Class Path
category: java
tags: [java]
---

# 说明

本文件将会持续更新

20220801更新一些基本的目录情况

后续会在每个子模块增加**实际应用中遇到的知识补充**



# 参考文档



==[语法学习部分](第13章 SQL Statements (https://dev.mysql.com/doc/refman/5.7/en/sql-statements.html))==

> 主要是5.7第十三章内容

==[语法学习部分中文推荐网站](https://www.1keydata.com/cn/sql/)==

==[简单在线输入语句学习平台](https://www.sqlteaching.com/)==





# SQL语法汇总





\{\}: 表示是必有的内容

\[\]: 表示的是可选的内容

|: 后面是前面的替代







## **板块1**：数据修改相关





### **语句1**：修改数据库（DataBase）

~~~sql
ALTER {DATABASE | SCHEMA} [db_name]
    alter_option ...
ALTER {DATABASE | SCHEMA} db_name
    UPGRADE DATA DIRECTORY NAME

alter_option: {
    [DEFAULT] CHARACTER SET [=] charset_name
  | [DEFAULT] COLLATE [=] collation_name
}
~~~



这是对数据库**表格结构**做出的改变，需要有几点注意：

* Alter_option: 改变选项，有多种不同的情境，包括修改*数据库字符集*
* 表格信息被存储在数据库目录的**db.opt**文件中
* **Alter Schema**常被视为Alter Database同义词







### **语句2**：修改事件（Event）



*event是**事件触发器**，具体可以参考[该网站](https://zhuanlan.zhihu.com/p/482700272)*





~~~sql
ALTER
    [DEFINER = user]
    EVENT event_name
    [ON SCHEDULE schedule]
    [ON COMPLETION [NOT] PRESERVE]
    [RENAME TO new_event_name]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'string']
    [DO event_body]
~~~







==具体例子==



*先创建一个event*

~~~sql
CREATE EVENT myevent
    ON SCHEDULE
      EVERY 6 HOUR
    COMMENT 'A sample comment.'
    DO
      UPDATE myschema.mytable SET mycol = mycol + 1;
~~~

> 可见之后的create event章节







**修改**：将myevent的触发时间从每6小时变成每12小时，而且从4小时后开始

~~~sql
ALTER EVENT myevent
    ON SCHEDULE
      EVERY 12 HOUR
    STARTS CURRENT_TIMESTAMP + INTERVAL 4 HOUR;
~~~



**修改**：不仅改时间，而且改内容

~~~sql
ALTER EVENT myevent
    ON SCHEDULE
      AT CURRENT_TIMESTAMP + INTERVAL 1 DAY
    DO
      TRUNCATE TABLE myschema.mytable;
~~~







**修改**：停用一个事件

~~~sql
ALTER TABLE myevent DISABLE
~~~







**修改**：重命名事件

~~~sql
ALTER EVENT olddb.myevent
    RENAME TO newdb.myevent;
~~~

> 这个写法同时把事件所在数据库进行转移









### **语句3**：修改函数（Function）



[自定义简单函数语法](https://www.cnblogs.com/m6Better/p/14939172.html)





~~~sql
ALTER FUNCTION func_name [characteristic ...]

characteristic: {
    COMMENT 'string'
  | LANGUAGE SQL
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
}
~~~



* 这个语句*无法改变函数的内部结构，与参数*
* 要实现上述修改，必须把函数drop，再重新定义







### **语句4**：修改实例（Instance）



*[关于实例和数据库的区别](https://blog.csdn.net/weixin_36399948/article/details/113441345)*

~~~sql
ALTER INSTANCE ROTATE INNODB MASTER KEY
~~~



*这个语句我还不太懂，只是做摘录*





### **语句5**：修改Logfile 群组（Logfile Group）



logfile group就是**日志管理文件组**

~~~sql
ALTER LOGFILE GROUP logfile_group
    ADD UNDOFILE 'file_name'
    [INITIAL_SIZE [=] size]
    [WAIT]
    ENGINE [=] engine_name
~~~





==具体例子==

~~~sql
ALTER LOGFILE GROUP lg_3
    ADD UNDOFILE 'undo_10.dat'
    INITIAL_SIZE=32M
    ENGINE=NDBCLUSTER;
~~~







### **语句6**：修改存储过程（procedure）



*[存储过程介绍](https://blog.csdn.net/weixin_30528371/article/details/101715404)*



~~~sql
ALTER PROCEDURE proc_name [characteristic ...]

characteristic: {
    COMMENT 'string'
  | LANGUAGE SQL
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
}
~~~







