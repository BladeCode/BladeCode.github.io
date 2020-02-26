---
title: MySQL 必备技能
date: 2019-11-01 00:01:00
categories: MySQL
tag: [MySQL]
---

![](https://res.cloudinary.com/incoder/image/upload/v1573002782/blog/mysql-skill.png)

<!-- more -->

## SQL 语句

SQL全称(Structured Query Language)：是一种特定目的编程语言，用于管理关系数据库管理系统（RDBMS），或在关系流数据管理系统（RDSMS）中进行流处理
也就是一种数据库查询和程序设计语言，用于存取数据以及查询和管理关系型数据库

### SQL 规则

1. SQL 语句可以单行或多行书写，以分号 `;` 结尾
2. 可以使用空格和缩进来增强语句可读性
3. MySQL数据库的 SQL 语句不区分大小写，**关键字建议大写**

### SQL 分类

![SQL_Commands](https://res.cloudinary.com/incoder/image/upload/v1582687197/blog/SQL_Commands.png)

#### DDL

Data Definition Language（DDL）：数据定义语言，用来创建数据库中的表，索引，视图，存储过程，触发器等。

##### 操作数据库

* CREATE：创建
   ```sql
   # 创建数据库
   CREATE DATABASE 数据库名称;
   # 创建数据库，判断是否存在，不存在则创建
   CREATE DATABASE IF NOT EXISTS 数据库名称;
   # 创建数据库并指定其字符集
   CREATE DATABASE 数据库名称 CHARACTER SET 字符集;
   ```
* ALERT：修改
   ```sql
   # 修改数据库的字符集
   ALTER DATABASE 数据库名称 CHARACTER SET 字符集名称;
   ```
* DROP：删除
   ```sql
   # 删除数据库
   DROP DATABASE 数据库名称;
   # 判断数据库存在，存在则删除
   DROP DATABASE IF EXISTS 数据库名称;
   ```
* 查询
   ```sql
   # 查询所有数据库的名称
   SHOW DATABASES;
   # 查询某个数据库的创建语句
   SHOW CREATE DATABASE 数据库名称;
   ```

##### 操作表

* CREATE：创建
   ```sql
   # 语法
   CREATE TABLE tableName(
        列名1 数据类型1,
        列名2 数据类型2,
        ....
        列名n 数据类型n,
        [添加约束...]
    );
    # 示例
   ```
* ALERT：修改
   ```sql
   # 修改表名
   ALTER TABLE 表名 RENAME TO 新表名;
   # 修改表的字符集
   ALTER TABLE 表名 CHARACTER SET 字符集名称;
   # 添加一列
   ALTER TABLE 表名 ADD 列名 数据类型;
   # 修改列名 类型
   ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型;
   # 只修改数据类型
   ALTER TABLE 表名 MODIFY 列名 新数据类型;
   # 删除列
   ALTER TABLE 表名 DROP 列名;
   ```
* DROP：删除
   ```sql
   # 删除表
   DROP TABLE 表名;
   # 判断表是否存在，存在则删除
   DROP TABLE IF EXISTS 表名;
   ```

>TRUNCATE 和 DELETE 区别
>1. TRUNCATE TABLE 表名 语句在功能上与不带 WHERE 子句的 DELETE 语句相同；二者均删除表中的全部数据，但 TRUNCATE TABLE 比 DELETE 速度快，且使用的系统和事务日志资源少
>2. DELETE 语句每次删除一行，并在事务日志中为所删除的每一行记录。TRUNCATE TABLE 通过释放存储表数据所用的数据页来删除数据，并且旨在事务日志中记录页的释放
>3. TRUNCATE TABLE 删除表中的所有行，但表结构及其列，约束，索引等保存不变，且会**重置表的计数**（通常我们作为表的主键）；DELETE TABLE **不会重置计数**；如果要删除表定义及其数据，使用 DROP TABLEE 语句

#### DML

Data  Manipulation Language（DML）：数据操作语言，用来改变数据库数据

* INSERT
   ```sql
   # 插入
   INSERT INTO 表名(字段列表) VALUES(值列表)
   # 拷贝表
   INSERT INTO 新表名 SELECT * FROM 表名;
   CREATE TABLE 新表名 LIKE 表名;
   ```

* UPDATE
   ```sql
   UPDATE 表名 SET 字段1=值1,字段n=值n [WHERE 条件] [ORDER BY 字段名 ASC|DESC] [LIMIT];
   ```
* DELETE
   ```sql
   DELETE FROM 表名 [WHERE 条件] [ORDER BY 字段名 ASC|DESC] [LIMIT];
   ```

#### DQL

Data Query Language（DDL）：数据查询语言，用于建立，修改，删除数据库中的各种对象

```sql
SELECT
column_1,column_2,...
FROM table_1
[INNER | LEFT |RIGHT] JOIN table_2 ON CONDITIONS
WHERE conditions
GROUP BY column_1
HAVING group_conditions
ORDER BY column limit offset,length
```

#### DCL

Data Control Language（DCL）：数据控制语言

* GRANT
* REVOKE

#### TCL

Transaction Control Language（TCL）： 事务控制语言，用于维护数据的一致性

### 索引

### 字符集

## 常用命令


## 锁表处理

### 方法一

1. 查看是否锁表
   ```sql
   show OPEN TABLES where In_use > 0;
   ```
2. 查看进程，查找被锁表的进程ID
	```sql
	show processlist;
	```
3. kill 锁表的进程 ID
   ```sql
   kill id;
   ```

### 方法二

1. 查看当前数据库的锁表情况
   ```sql
   SELECT * FROM information_schema.INNODB_TRX;
   ```
2. 杀掉查询结果中锁表的trx_mysql_thread_id
   ```sql
   kill trx_mysql_thread_id
   ```

## MySQL 用户分配

![mysql-account](https://res.cloudinary.com/incoder/image/upload/v1574820841/blog/mysql-account.png)

## 参考

* [再见乱码：5分钟读懂MySQL字符集设置](https://www.cnblogs.com/chyingp/p/mysql-character-set-collation.html)