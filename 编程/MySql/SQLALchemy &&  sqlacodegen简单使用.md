

# SQLALchemy &&  sqlacodegen简单使用 和 踩坑

## sqlacodegen

### 使用流程

> 1. 安装

```
pip install sqlacodegen
```

> 2. 使用：生成整个数据库的models：

```
sqlacodegen --outfile=models.py mysql://root:" "@192.168.17.86:3306/learn
```

会在当前目录下生成`models.py`

数据库为"mysql",如果需要其他的数据库可以参考sqlacodegen官网，（PS：其实是通过SQLALchemy来连接的）

![](https://gitee.com/biepin/imgurl/raw/master/20201211104919.png)

生成文件如下：

![](https://gitee.com/biepin/imgurl/raw/master/20201211105112.png)

> 3 使用：生成数据库下的指定表

```
sqlacodegen --outfile=models1.py mysql://root:" "@192.168.17.86:3306/learn --tables customers
sqlacodegen --outfile=models2.py mysql://root:" "@192.168.17.86:3306/learn --tables customers,products
```

 以及最重要的**报错**：**ModuleNotFoundError: No module named 'MySQLdb'**

```
zerotwo@bogon Downloads % sqlacodegen --outfile=models.py mysql://root:" "@192.168.17.86:3306/learn --tables customers
Traceback (most recent call last):
  File "/usr/local/bin/sqlacodegen", line 8, in <module>
    sys.exit(main())
  File "/usr/local/lib/python3.9/site-packages/sqlacodegen/main.py", line 44, in main
    engine = create_engine(args.url)
  File "/usr/local/lib/python3.9/site-packages/sqlalchemy/engine/__init__.py", line 500, in create_engine
    return strategy.create(*args, **kwargs)
  File "/usr/local/lib/python3.9/site-packages/sqlalchemy/engine/strategies.py", line 87, in create
    dbapi = dialect_cls.dbapi(**dbapi_args)
  File "/usr/local/lib/python3.9/site-packages/sqlalchemy/dialects/mysql/mysqldb.py", line 118, in dbapi
    return __import__("MySQLdb")
ModuleNotFoundError: No module named 'MySQLdb'
zerotwo@bogon Downloads % sqlacodegen --outfile=models.py mysql://root:" "@192.168.17.86:3306/learn                   
Traceback (most recent call last):
  File "/usr/local/bin/sqlacodegen", line 8, in <module>
    sys.exit(main())
  File "/usr/local/lib/python3.9/site-packages/sqlacodegen/main.py", line 44, in main
    engine = create_engine(args.url)
  File "/usr/local/lib/python3.9/site-packages/sqlalchemy/engine/__init__.py", line 500, in create_engine
    return strategy.create(*args, **kwargs)
  File "/usr/local/lib/python3.9/site-packages/sqlalchemy/engine/strategies.py", line 87, in create
    dbapi = dialect_cls.dbapi(**dbapi_args)
  File "/usr/local/lib/python3.9/site-packages/sqlalchemy/dialects/mysql/mysqldb.py", line 118, in dbapi
    return __import__("MySQLdb")
ModuleNotFoundError: No module named 'MySQLdb'

```

因为没有`MySQLdb`，但是我们有`pymysql`,进入到最后报错的文件（上面的报错24行）

```
File "/usr/local/lib/python3.9/site-packages/sqlalchemy/dialects/mysql/mysqldb.py", line 118, in dbapi
    return __import__("MySQLdb")
```

修改`/usr/local/lib/python3.9/site-packages/sqlalchemy/dialects/mysql/mysqldb.py`中的第118行（2020.12.11）,修改为：

```
    return __import__("pymysql")
```

修改后应该是：

```
@classmethod
def dbapi(cls):
    return __import__("pymysql")
```

保存后，运行sqlacodegen便不再报错了

## SQLALchemy

略



## pymysql 的 连接池  DBUtils





