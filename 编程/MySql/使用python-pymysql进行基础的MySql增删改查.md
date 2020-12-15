[TOC]

# 0 准备工作

## 使用 pymysql 包

安装，升级，删除  pymysql 包：

```
pip install pymysql
pip install --upgrade pymysql
pip uninstall pymysql
```

# 1 查询操作

基础的查询代码如下：

```
import pymysql.cursors

# Connect to the database
connection = pymysql.connect(host='128.0.0.1',  # 配置host地址 
                             user='user',  # 数据库连接用户名 
                             password='password',
                             db='db_test',  # database名称
                             charset='utf8mb4',
                             cursorclass=pymysql.cursors.DictCursor)

try:
    with connection.cursor() as cursor:
        # Read a single record
        sql = "SELECT `id`, `name` FROM `base_project` WHERE `id`=%s"  # 书写sql语句
        cursor.execute(sql, 28)  # 执行sql语句，其参数若是多条，可以通过args来传入
        result = cursor.fetchone()  # 取出执行结果（单条） 若是需要取出多条数据 使用 for in cursor 取出
        print(result)
finally:
    connection.close()

```

以下是代码步骤的分解讲解：

导入需要的`pymysql`包：

```
import pymysql.cursors
```

建立与数据库的连接：

```
connection = pymysql.connect(host='128.0.0.1',  # 配置host地址 
                             user='user',  # 数据库连接用户名 
                             password='password',
                             db='db_test',  # database名称
                             charset='utf8mb4',
                             cursorclass=pymysql.cursors.DictCursor)
```

执行查询sql语句，并取出结果输入：

```
try:
    with connection.cursor() as cursor:
        # Read a single record
        sql = "SELECT `id`, `name` FROM `base_project` WHERE `id`=%s"  # 书写sql语句
        cursor.execute(sql, 28)  # 执行sql语句，其参数若是多条，可以通过args来传入
        result = cursor.fetchone()  # 取出执行结果（单条） 若是需要取出多条数据 使用 for in cursor 取出
        print(result)
```

**注意！！！**：一定要在操作完成后关闭与数据库的连接：

```
finally:
    connection.close()
```

## 插入操作

需要在 `cursor.execute()` 后执行 `connection.commit()` 来向数据库提交