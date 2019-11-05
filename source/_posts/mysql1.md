---
title: MySQL 必备技能
date: 2019-11-01 00:01:00
categories: MySQL
tag: [MySQL]
---

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
