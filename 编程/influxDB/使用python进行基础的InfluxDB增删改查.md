[TOC]

**本文写成时间2020-12-01**

# 0 准备工作
## 使用的InnfluxDB-Python包

安装，升级，删除  influxdb 包：

```
pip install influxdb
pip install --upgrade influxdb
pip uninstall influxdb
```



# 1 连接InfluxDB

基础的查询代码如下：

```
import json
from influxdb import InfluxDBClient
from influxdb import DataFrameClient

# 建立InfluxDBClient客户端连接 参数分别为：host,port,username,password,database_name
client = InfluxDBClient('127.0.0.1', 8086, 'admin', 'admin', 'test')
# 建立DataFrameClient客户端连接 参数分别为：host,port,username,password,database_name
client2 = DataFrameClient('127.0.0.1', 8086, 'admin', 'admin', 'test')


# 取出InfluxDBClient执行query语句后的结果
result = client.query("select * from mean_qz limit 5 tz('Asia/Shanghai') ;")
# 由于结果是influxdb定义的ResultSet类，不好解析，转为json格式
print(json.dumps(result.__dict__))

# 取出DataFrameClient执行query语句后的结果,其类型为DataFrame类型
result2 = client2.query("select * from mean_qz limit 5 tz('Asia/Shanghai') ;")
print(result2)
```

以下是代码步骤的分解讲解：

导入操作InfluxDB需要的`pymysql`包，为了方便数据解析的`json`包：

```
import json
from influxdb import InfluxDBClient
from influxdb import DataFrameClient
```

连接数据库：

```
# 建立InfluxDBClient客户端连接 参数分别为：host,port,username,password,database_name
client = InfluxDBClient('127.0.0.1', 8086, 'admin', 'admin', 'test')
# 建立DataFrameClient客户端连接 参数分别为：host,port,username,password,database_name
client2 = DataFrameClient('127.0.0.1', 8086, 'admin', 'admin', 'test')
```

执行查询语句并获取结果输出：

```
# 取出InfluxDBClient执行query语句后的结果
result = client.query("select * from mean_qz limit 5 tz('Asia/Shanghai') ;")
# 由于结果是influxdb定义的ResultSet类，不好解析，转为json格式
print(json.dumps(result.__dict__))

# 取出DataFrameClient执行query语句后的结果,其类型为DataFrame类型
result2 = client2.query("select * from mean_qz limit 5 tz('Asia/Shanghai') ;")
print(result2)
```

