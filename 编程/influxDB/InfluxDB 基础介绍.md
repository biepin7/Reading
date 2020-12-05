[TOC]



# InfluxDB 1.6

# 0 安装 和 准备工作

nohup chronograf > /dev/null 2>&1 &

## 0.1 安装

无

## 0.2 启动CLI命令 influx

键入**influx**

```
[root@localhost ~]# influx
Connected to http://localhost:8086 version 1.6.1
InfluxDB shell version: 1.6.1
>
```

可以输入`help`查看命令列表帮助:

```
> help
Usage:
        connect <host:port>   connects to another node specified by host:port
        auth                  prompts for username and password
        pretty                toggles pretty print for the json format
        chunked               turns on chunked responses from server
        chunk size <size>     sets the size of the chunked responses.  Set to 0 to reset to the default chunked size
        use <db_name>         sets current database
        format <format>       specifies the format of the server responses: json, csv, or column
        precision <format>    specifies the format of the timestamp: rfc3339, h, m, s, ms, u or ns
        consistency <level>   sets write consistency level: any, one, quorum, or all
        history               displays command history
        settings              outputs the current settings for the shell
        clear                 clears settings such as database or retention policy.  run 'clear' for help
        exit/quit/ctrl+d      quits the influx shell

        show databases        show database names
        show series           show series information
        show measurements     show measurement information
        show tag keys         show tag key information
        show field keys       show field key information

        A full list of influxql commands can be found at:
        https://docs.influxdata.com/influxdb/latest/query_language/spec/
```

## 样例数据准备

首先创建数据库：

```
[root@localhost ~]# influx
Connected to http://localhost:8086 version 1.6.1
InfluxDB shell version: 1.6.1
> exit
```

然后下载：

```
curl https://s3.amazonaws.com/noaa.water-database/NOAA_data.txt -o NOAA_data.txt
```

PS:在国内巨慢无比

下载完成后写入influxDB：

```
influx -import -path=NOAA_data.txt -precision=s -database=NOAA_water_database
```

查询样例：

连接数据库：

```
$ influx -precision rfc3339 -database NOAA_water_database
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x
>
```

See all five measurements:

```
SHOW measurements
```

```
> SHOW measurements
name: measurements
name
----
average_temperature
h2o_feet
h2o_pH
h2o_quality
h2o_temperature
```

Count the number of non-null values of `water_level` in `h2o_feet` / 计算`water_level`in中非空值的数量`h2o_feet`:

```
SELECT COUNT("water_level") FROM h2o_feet
```

```
> SELECT COUNT("water_level") FROM h2o_feet
name: h2o_feet
time                 count
----                 -----
1970-01-01T00:00:00Z 15258
```

Select the first five observations in the measurement `h2o_feet` / 选择测量``h2o_feet`中的前五个观测值:

```
SELECT * FROM h2o_feet LIMIT 5
```

```
> SELECT * FROM h2o_feet LIMIT 5
name: h2o_feet
time                 level description    location     water_level
----                 -----------------    --------     -----------
2019-08-17T00:00:00Z below 3 feet         santa_monica 2.064
2019-08-17T00:00:00Z between 6 and 9 feet coyote_creek 8.12
2019-08-17T00:06:00Z below 3 feet         santa_monica 2.116
2019-08-17T00:06:00Z between 6 and 9 feet coyote_creek 8.005
2019-08-17T00:12:00Z below 3 feet         santa_monica 2.028
```

# Infulx Query Language （InfluxQL）

## 