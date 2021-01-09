[TOC]

# About 准备工作

首先 **附录 B 样 例 表** 的网址里有：https://forta.com/books/0672327120/ 可以下载`mysql_scripts`解压后得到：

- `create.sql`:包含创建6个数据库表（包括所有主键和外键约束）的MySQL语句
- `populate.sql`:包含用来填充这些表的INSERT语句







# 章一 了解SQL 

## 1.1 数据库基础





# 章三 使用MySql

## 3.1 连接

为了连接到MySQL，需要以下信息：

- 主机名
- 端口
- 用户名
- 用户口令

设置密码后:
```
mysql -p 
```

## 3.2 选择数据库 USE关键字

**use xxx**

```
mysql> USE learn
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

## 3.3 了解数据库和表 SHOW

**SHOW DATABASES**

```
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| learn              |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

**SHOW TABLES;**

```
mysql> SHOW TABLES;
+-----------------+
| Tables_in_learn |
+-----------------+
| customers       |
| orderitems      |
| orders          |
| productnotes    |
| products        |
| vendors         |
+-----------------+
6 rows in set (0.00 sec)
```

**SHOW COLUMNS FROM xxxx**

```
mysql> SHOW COLUMNS FROM customers;
+--------------+-----------+------+-----+---------+----------------+
| Field        | Type      | Null | Key | Default | Extra          |
+--------------+-----------+------+-----+---------+----------------+
| cust_id      | int(11)   | NO   | PRI | NULL    | auto_increment |
| cust_name    | char(50)  | NO   |     | NULL    |                |
| cust_address | char(50)  | YES  |     | NULL    |                |
| cust_city    | char(50)  | YES  |     | NULL    |                |
| cust_state   | char(5)   | YES  |     | NULL    |                |
| cust_zip     | char(10)  | YES  |     | NULL    |                |
| cust_country | char(50)  | YES  |     | NULL    |                |
| cust_contact | char(50)  | YES  |     | NULL    |                |
| cust_email   | char(255) | YES  |     | NULL    |                |
+--------------+-----------+------+-----+---------+----------------+
9 rows in set (0.00 sec)
```

以上的`SHOW`来查看数据表，还有其他的`SHOW`:

- `SHOW STATUS`:用于显示广泛的服务器状态信息

- `SHOW CREATE DATABASE`和`SHOW CREATE TABLE`，分别用来显示建特定数据库或表的MySQL语句

- `SHOW GRANTS`:用来显示授予用户（所有用户或特定用户）的安全权限

- `SHOW ERRORS`和`SHOW WARNINGS`:用来显示服务器错误或警告消息

```
mysql> SHOW ERRORS;
Empty set (0.00 sec)

mysql> SHOW WARNINGS;
Empty set (0.00 sec)
```

也可以使用`HELP SHOW`查看具体细节

# 章四 检索数据 SELECT

## 4.2 检索单个列

```
mysql> SELECT prod_name FROM products;
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Detonator      |
| Bird seed      |
| Carrots        |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
14 rows in set (0.00 sec)
```

PS:这里的`SELECT`语句是没有经过排序的（同样的语句返回结果不同，又可能是被添加到表的顺序） 

## 4.3 检索多个列



```
mysql> SELECT prod_id,prod_name,prod_price  FROM products;
+---------+----------------+------------+
| prod_id | prod_name      | prod_price |
+---------+----------------+------------+
| ANV01   | .5 ton anvil   |       5.99 |
| ANV02   | 1 ton anvil    |       9.99 |
| ANV03   | 2 ton anvil    |      14.99 |
| DTNTR   | Detonator      |      13.00 |
| FB      | Bird seed      |      10.00 |
| FC      | Carrots        |       2.50 |
| FU1     | Fuses          |       3.42 |
| JP1000  | JetPack 1000   |      35.00 |
| JP2000  | JetPack 2000   |      55.00 |
| OL1     | Oil can        |       8.99 |
| SAFE    | Safe           |      50.00 |
| SLING   | Sling          |       4.49 |
| TNT1    | TNT (1 stick)  |       2.50 |
| TNT2    | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
14 rows in set (0.00 sec)
```



## 4.4 检索所有列 SELECT *



```
mysql> SELECT *  FROM products;
+---------+---------+----------------+------------+----------------------------------------------------------------+
| prod_id | vend_id | prod_name      | prod_price | prod_desc                                                      |
+---------+---------+----------------+------------+----------------------------------------------------------------+
| ANV01   |    1001 | .5 ton anvil   |       5.99 | .5 ton anvil, black, complete with handy hook                  |
| ANV02   |    1001 | 1 ton anvil    |       9.99 | 1 ton anvil, black, complete with handy hook and carrying case |
| ANV03   |    1001 | 2 ton anvil    |      14.99 | 2 ton anvil, black, complete with handy hook and carrying case |
| DTNTR   |    1003 | Detonator      |      13.00 | Detonator (plunger powered), fuses not included                |
| FB      |    1003 | Bird seed      |      10.00 | Large bag (suitable for road runners)                          |
| FC      |    1003 | Carrots        |       2.50 | Carrots (rabbit hunting season only)                           |
| FU1     |    1002 | Fuses          |       3.42 | 1 dozen, extra long                                            |
| JP1000  |    1005 | JetPack 1000   |      35.00 | JetPack 1000, intended for single use                          |
| JP2000  |    1005 | JetPack 2000   |      55.00 | JetPack 2000, multi-use                                        |
| OL1     |    1002 | Oil can        |       8.99 | Oil can, red                                                   |
| SAFE    |    1003 | Safe           |      50.00 | Safe with combination lock                                     |
| SLING   |    1003 | Sling          |       4.49 | Sling, one size fits all                                       |
| TNT1    |    1003 | TNT (1 stick)  |       2.50 | TNT, red, single stick                                         |
| TNT2    |    1003 | TNT (5 sticks) |      10.00 | TNT, red, pack of 10 sticks                                    |
+---------+---------+----------------+------------+----------------------------------------------------------------+
14 rows in set (0.00 sec)
```



## 4.5 检索不同的行 DISTINCT

```
mysql> SELECT DISTINCT vend_id
    -> FROM products;
+---------+
| vend_id |
+---------+
|    1001 |
|    1002 |
|    1003 |
|    1005 |
+---------+
4 rows in set (0.00 sec)
```

## 4.6 限制结果 LIMIT

```
mysql> SELECT prod_name 
    -> FROM products
    -> LIMIT 5;
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
| Detonator    |
| Bird seed    |
+--------------+
5 rows in set (0.00 sec)
```

为得出下一个5行，可指定要检索的开始行和行数

```
mysql> SELECT prod_name  FROM products LIMIT 5,5;
+--------------+
| prod_name    |
+--------------+
| Carrots      |
| Fuses        |
| JetPack 1000 |
| JetPack 2000 |
| Oil can      |
+--------------+
5 rows in set (0.00 sec)
```

> 1.行是从0开始数的 ，LIMIT 1，1 :第二行
>
> 2.只显示数据库里那么多的行
>
> 3.防止混淆：LIMIT 3, 4 = LIMIT 4 OFFSET 3

## 4.7 使用完全限定的表名

```
mysql> SELECT products.prod_name
    -> FROM products;
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Detonator      |
| Bird seed      |
| Carrots        |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
14 rows in set (0.00 sec)
```

# 章五 排序检索数据 ORDERBY

## 5.1 排序数据

```
mysql> SELECT prod_name
    -> FROM products
    -> ORDER BY prod_name;
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Bird seed      |
| Carrots        |
| Detonator      |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
14 rows in set (0.00 sec)
```

> 可以使用未检索的列来排序数据

## 5.2 按多个列排序

解在按多个列排序时，排序完全按所规定的顺序进行

```
mysql>SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price,prod_name;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| FC      |       2.50 | Carrots        |
| TNT1    |       2.50 | TNT (1 stick)  |
| FU1     |       3.42 | Fuses          |
| SLING   |       4.49 | Sling          |
| ANV01   |       5.99 | .5 ton anvil   |
| OL1     |       8.99 | Oil can        |
| ANV02   |       9.99 | 1 ton anvil    |
| FB      |      10.00 | Bird seed      |
| TNT2    |      10.00 | TNT (5 sticks) |
| DTNTR   |      13.00 | Detonator      |
| ANV03   |      14.99 | 2 ton anvil    |
| JP1000  |      35.00 | JetPack 1000   |
| SAFE    |      50.00 | Safe           |
| JP2000  |      55.00 | JetPack 2000   |
+---------+------------+----------------+
14 rows in set (0.00 sec)
```

## 5.3 指定排序方向 DESC

```
mysql> SELECT prod_id.prod_price,prod_name
    -> FROM products
    -> ORDER BY prod_price DESC;
ERROR 1054 (42S22): Unknown column 'prod_id.prod_price' in 'field list'
mysql> SELECT prod_id ,prod_price ,prod_name
    -> FROM products
    -> ORDER BY prod_price DESC;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| JP2000  |      55.00 | JetPack 2000   |
| SAFE    |      50.00 | Safe           |
| JP1000  |      35.00 | JetPack 1000   |
| ANV03   |      14.99 | 2 ton anvil    |
| DTNTR   |      13.00 | Detonator      |
| FB      |      10.00 | Bird seed      |
| TNT2    |      10.00 | TNT (5 sticks) |
| ANV02   |       9.99 | 1 ton anvil    |
| OL1     |       8.99 | Oil can        |
| ANV01   |       5.99 | .5 ton anvil   |
| SLING   |       4.49 | Sling          |
| FU1     |       3.42 | Fuses          |
| FC      |       2.50 | Carrots        |
| TNT1    |       2.50 | TNT (1 stick)  |
+---------+------------+----------------+
14 rows in set (0.00 sec)
```

只对prod_price列指定DESC，对prod_name列不指定。

> DESC关键字只应用到直接位于其前面的列名

> 排序默认是字典排序，要修改要找数据库管理员

> 在给出ORDER BY子句时，应该保证它位于FROM子句之后。如果使用LIMIT，它必须位于ORDER BY之后。
>
> FROM > ORDER BY > LIMIT

# 章六 过滤数据 WHERE

## 6.1 使用WHERE子句



```
mysql> SELECT prod_name,prod_price FROM products WHERE prod_price =2.5;+---------------+------------+
| prod_name     | prod_price |
+---------------+------------+
| Carrots       |       2.50 |
| TNT (1 stick) |       2.50 |
+---------------+------------+
2 rows in set (0.01 sec)
```

> 在同时使用ORDER BY和WHERE子句时，应该让ORDER BY位于WHERE之后，否则将会产生错误
>
> WHERE > ORDER BY

## 6.2 WHERE子句操作符

| 操作符  | 说明 |
| ------- | ---- |
| =       |      |
| <>      |      |
| !=      |      |
| <       |      |
| <=      |      |
| >       |      |
| >=      |      |
| BETWEEN |      |

```
mysql> SELECT prod_name ,prod_price
    -> FROM products
    -> WHERE prod_name ='fuses';
+-----------+------------+
| prod_name | prod_price |
+-----------+------------+
| Fuses     |       3.42 |
+-----------+------------+
1 row in set (0.00 sec)
```

```
mysql> SELECT prod_name,prod_price
    -> FROM products
    -> WHERE prod_price <10;
+---------------+------------+
| prod_name     | prod_price |
+---------------+------------+
| .5 ton anvil  |       5.99 |
| 1 ton anvil   |       9.99 |
| Carrots       |       2.50 |
| Fuses         |       3.42 |
| Oil can       |       8.99 |
| Sling         |       4.49 |
| TNT (1 stick) |       2.50 |
+---------------+------------+
7 rows in set (0.00 sec)
```

```
mysql> SELECT vend_id,prod_name
    -> FROM products
    -> WHERE vend_id <> 1003;
+---------+--------------+
| vend_id | prod_name    |
+---------+--------------+
|    1001 | .5 ton anvil |
|    1001 | 1 ton anvil  |
|    1001 | 2 ton anvil  |
|    1002 | Fuses        |
|    1005 | JetPack 1000 |
|    1005 | JetPack 2000 |
|    1002 | Oil can      |
+---------+--------------+
7 rows in set (0.00 sec)
```

```
mysql> SELECT prod_name,prod_price
    -> FROM products
    -> WHERE prod_price BETWEEN 5 AND 10;
+----------------+------------+
| prod_name      | prod_price |
+----------------+------------+
| .5 ton anvil   |       5.99 |
| 1 ton anvil    |       9.99 |
| Bird seed      |      10.00 |
| Oil can        |       8.99 |
| TNT (5 sticks) |      10.00 |
+----------------+------------+
5 rows in set (0.00 sec)
```

### 6.2.4 空值检查





# 章 三十 改善性能

## 30.1 改善性能

1. 特定的硬件建议
2. 关键的DNMS应该运行在自己的专用服务器上
3. MySql用一系列的默认配置，所以可以根据自己的需要进行调整（内存分配，缓冲区大小）（查看当前设置：`SHOW VARIABLLES` `SHOW STATUS`
4. MySql 是一个多用户多线程的DBMS，一个任务执行缓慢，所有的请求都会执行缓慢，可以使用`SHOW PROCESSLIST`查看所有的活动进程 用`KILL`结束特定进程
5. 尝试修改`SELECT`语句
6. 使用`EXPLAN`语句让`MySQL`解释它如何执行一条`SELECT`语句
7. 一般来说，**存储过程**执行得比一条一条地执行其中的各条MySQL语句快
8. 总使用正确的数据类型
9. 不要检索比需要还要多的数据
10. 在导入数据时，应该关闭自动提交。你可能还想删除索引（包括FULLTEXT索引），然后在导入完成后再重建它们。？？？
11. 必须索引数据库表以改善数据检索的性能，需要分析使用的SELECT语句以找出重复的WHERE和ORDER BY子句
12. 使用多条SELECT和UNION语句来替换复杂条件的OR
13. 索引改善数据检索的性能，但是有损插入，删除和更新的性能。收集数据且不经常被搜索的表在有必要前，不要索引它们
14. LIKE很慢，最好是使用FULLTEXT而不是LIKE
15. 数据库是不断变化的实体。一组优化良好的表一会儿后可能就面目全非了。由于表的使用和内容的更改，理想的优化和配置也会改变
16. 每条规则在某些条件下都会被打破

