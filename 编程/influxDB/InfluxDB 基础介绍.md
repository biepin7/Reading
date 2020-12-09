[TOC]



# InfluxDB 1.6

# 零 安装 和 准备工作

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

## 0.3 样例数据准备

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

### 连接数据库：

```
$ influx -precision rfc3339 -database NOAA_water_database
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x
>
```

### See all five measurements: 以及 查看单个measurement 中的tags 和 field

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

附加一点东西  `show tag`和`show field`

```
> show tag keys FROM h2o_feet
name: h2o_feet
tagKey
------
location
> show field keys from h2o_feet
name: h2o_feet
fieldKey          fieldType
--------          ---------
level description string
water_level       float
```





**Count the number of non-null values of `water_level` in `h2o_feet` / 计算`water_level`in中非空值的数量`h2o_feet`:**

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

**Select the first five observations in the measurement `h2o_feet` / 选择测量``h2o_feet`中的前五个观测值:**

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

# 一 Infulx Query Language （InfluxQL）

## 1.1 基础准备

使用的是 0.3 中的数据库`NOAA_water_database`中的`h2o_feet`measurement

|         time         | level description    | location     | water_level |
| :------------------: | -------------------- | ------------ | ----------- |
| 2015-08-18T00:00:00Z | between 6 and 9 feet | coyote_creek | 8.12        |
| 2015-08-18T00:00:00Z | below 3 feet         | santa_monica | 2.064       |
| 2015-08-18T00:06:00Z | between 6 and 9 feet | coyote_creek | 8.005       |
| 2015-08-18T00:06:00Z | below 3 feet         | santa_monica | 2.116       |
| 2015-08-18T00:12:00Z | between 6 and 9 feet | coyote_creek | 7.887       |
| 2015-08-18T00:12:00Z | below 3 feet         | santa_monica | 2.028       |

The data in the `h2o_feet` measurement occur at six-minute time intervals. 
measurement中的数据每隔6min出现一次

The measurement has one **tag key 标签键** (`location`) which has two **tag values标签值**:`coyote_creek` and `santa_monica`. 



The measurement also has two **fields 字段**: `level description` stores string field values and `water_level` stores float field values. 

## 1.2 The basic SELECT statement 基本SELECT语句

### Syntax

```
SELECT <field_key>[,<field_key>,<tag_key>] FROM <measurement_name>[,<measurement_name>]
```

### Description of syntax

The `SELECT` statement requires a `SELECT` clause and a `FROM` clause.

| 语句                                         | 功能                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| SELECT *                                     | Returns all fields and tags.                                 |
| SELECT "<field_key>"                         | Returns a specific field.                                    |
| SELECT "<field_key>","<field_key>"           | Returns more than one field.                                 |
| SELECT "<field_key>","<tag_key>"             | Returns a specific field and tag. The SELECT clause must specify **at least one field** when it includes a tag. |
| SELECT "<field_key>"::field,"<tag_key>"::tag | Returns a specific field and tag                             |

The` ::[field | tag]` syntax specifies the identifier’s type. Use this syntax to differentiate between field keys and tag keys that have the same name.

Other supported features: **Arithmetic operations 算数运算, Functions函数, Basic cast operations基本类型转换, Regular expressions 正则表达式**

| 语句                                                         | 功能                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| FROM <measurement_name>                                      | Returns data from a single measurement.                      |
| FROM <measurement_name>,<measurement_name>                   | Returns data from more than one measurement.                 |
| FROM <database_name>.<retention_policy_name>.<measurement_name> | Returns data from a fully qualified measurement. Fully qualify a measurement by specifying its database and retention policy. |
| FROM <database_name>..<measurement_name>                     | Returns data from a measurement in a user-specified database and the DEFAULT retention policy. |

If you’re using the `CLI` InfluxDB queries the measurement in the USEd database and the DEFAULT retention policy. 

If you’re using the `HTTP API` InfluxDB queries the measurement in the database specified in the db query string parameter and the DEFAULT retention policy.

### Examples

```
 SELECT * FROM "h2o_feet"
 # Select all fields and tags from a single measurement
 
 
 > SELECT * FROM "h2o_feet"

name: h2o_feet
--------------
time                   level description      location       water_level
2015-08-18T00:00:00Z   below 3 feet           santa_monica   2.064
2015-08-18T00:00:00Z   between 6 and 9 feet   coyote_creek   8.12
[...]
2015-09-18T21:36:00Z   between 3 and 6 feet   santa_monica   5.066
2015-09-18T21:42:00Z   between 3 and 6 feet   santa_monica   4.938
```

If you’re using the CLI be sure to enter USE NOAA_water_database before you run the query. The CLI queries the data in the USEd database and the DEFAULT retention policy. If you’re using the HTTP API be sure to set the db query string parameter to NOAA_water_database. If you do not set the rp query string parameter, the HTTP API automatically queries the database’s DEFAULT retention policy.

```
 SELECT "level description","location","water_level" FROM "h2o_feet"
//Select specific tags and fields from a single measurement

> SELECT "level description","location","water_level" FROM "h2o_feet"

name: h2o_feet
--------------
time                   level description      location       water_level
2015-08-18T00:00:00Z   below 3 feet           santa_monica   2.064
2015-08-18T00:00:00Z   between 6 and 9 feet   coyote_creek   8.12
[...]
2015-09-18T21:36:00Z   between 3 and 6 feet   santa_monica   5.066
2015-09-18T21:42:00Z   between 3 and 6 feet   santa_monica   4.938
```

需要注意：

Note that the `SELECT` clause must specify at least one field when it includes a tag.

`SELECT`子句包含标签时，必须至少指定一个tag      

```
SELECT *::field FROM "h2o_feet"
# Select all fields from a single measurement

> SELECT *::field FROM "h2o_feet"

name: h2o_feet
--------------
time                   level description      water_level
2015-08-18T00:00:00Z   below 3 feet           2.064
2015-08-18T00:00:00Z   between 6 and 9 feet   8.12
[...]
2015-09-18T21:36:00Z   between 3 and 6 feet   5.066
2015-09-18T21:42:00Z   between 3 and 6 feet   4.938
```

```
SELECT ("water_level" * 2) + 4 from "h2o_feet"
# Select a specific field from a measurement and perform basic arithmetic


> SELECT ("water_level" * 2) + 4 from "h2o_feet"

name: h2o_feet
--------------
time                   water_level
2015-08-18T00:00:00Z   20.24
2015-08-18T00:00:00Z   8.128
[...]
2015-09-18T21:36:00Z   14.132
2015-09-18T21:42:00Z   13.876
```

```
 SELECT * FROM "h2o_feet","h2o_pH"
# Select all data from more than one measurement
> SELECT * FROM "h2o_feet","h2o_pH"

name: h2o_feet
--------------
time                   level description      location       pH   water_level
2015-08-18T00:00:00Z   below 3 feet           santa_monica        2.064
2015-08-18T00:00:00Z   between 6 and 9 feet   coyote_creek        8.12
[...]
2015-09-18T21:36:00Z   between 3 and 6 feet   santa_monica        5.066
2015-09-18T21:42:00Z   between 3 and 6 feet   santa_monica        4.938

name: h2o_pH
------------
time                   level description   location       pH   water_level
2015-08-18T00:00:00Z                       santa_monica   6
2015-08-18T00:00:00Z                       coyote_creek   7
[...]
2015-09-18T21:36:00Z                       santa_monica   8
2015-09-18T21:42:00Z                       santa_monica   7
```

## 1.3 The WHERE clause

### Syntax

```
SELECT_clause FROM_clause WHERE <conditional_expression> [(AND|OR) <conditional_expression> [...]]
```

### Description of syntax

The `WHERE` clause supports `conditional_expression`s on **fields, tags, and timestamps.**

**Note InfluxDB does not support using OR in the WHERE clause to specify multiple time ranges.**

**注意InfluxDB不支持在WHERE子句中使用OR来指定多个时间范围**

#### Fields

```
field_key <operator> ['string' | boolean | float | integer]
```

The WHERE clause supports comparisons against **string, boolean, float, and integer field values.**

Single quote string field values in the WHERE clause. Queries with unquoted string field values or double quoted string field values will not return any data and, in most cases, will not return an error.

所有的where条件都应该加上单引号`'`

#### Tags

```
tag_key <operator> ['tag_value']
```

#### Timestamps

For most SELECT statements, the default time range is between `1677-09-21 00:12:43.145224194` and `2262-04-11T23:47:16.854775806Z` UTC.

 For SELECT statements with a` GROUP BY time()` clause, the default time range is between `1677-09-21 00:12:43.145224194` UTC and now().

### Examples

Select data that **have specific field key-values** 

```
> SELECT * FROM "h2o_feet" WHERE "water_level" > 8

name: h2o_feet
--------------
time                   level description      location       water_level
2015-08-18T00:00:00Z   between 6 and 9 feet   coyote_creek   8.12
2015-08-18T00:06:00Z   between 6 and 9 feet   coyote_creek   8.005
[...]
2015-09-18T00:12:00Z   between 6 and 9 feet   coyote_creek   8.189
2015-09-18T00:18:00Z   between 6 and 9 feet   coyote_creek   8.084
```

Select data that have **a specific string field key-value**

```
> SELECT * FROM "h2o_feet" WHERE "level description" = 'below 3 feet'

name: h2o_feet
--------------
time                   level description   location       water_level
2015-08-18T00:00:00Z   below 3 feet        santa_monica   2.064
2015-08-18T00:06:00Z   below 3 feet        santa_monica   2.116
[...]
2015-09-18T14:06:00Z   below 3 feet        santa_monica   2.999
2015-09-18T14:36:00Z   below 3 feet        santa_monica   2.907
```

Select data that have a specific field key-value and perform basic arithmetic

```
> SELECT * FROM "h2o_feet" WHERE "water_level" + 2 > 11.9
name: h2o_feet
time                level description         location     water_level
----                -----------------         --------     -----------
1566975960000000000 at or greater than 9 feet coyote_creek 9.902
1566976320000000000 at or greater than 9 feet coyote_creek 9.938
1566976680000000000 at or greater than 9 feet coyote_creek 9.957
1566977040000000000 at or greater than 9 feet coyote_creek 9.964
1566977400000000000 at or greater than 9 feet coyote_creek 9.954
1566977760000000000 at or greater than 9 feet coyote_creek 9.941
1566978120000000000 at or greater than 9 feet coyote_creek 9.925
1566978480000000000 at or greater than 9 feet coyote_creek 9.902
1567380600000000000 at or greater than 9 feet coyote_creek 9.902
```

Select data that have a specific tag key-value

```
> SELECT "water_level" FROM "h2o_feet" WHERE "location" = 'santa_monica'

name: h2o_feet
--------------
time                   water_level
2015-08-18T00:00:00Z   2.064
2015-08-18T00:06:00Z   2.116
[...]
2015-09-18T21:36:00Z   5.066
2015-09-18T21:42:00Z   4.938
```

Select data that **have specific field key-values and tag key-values**

```
> SELECT "water_level" FROM "h2o_feet" WHERE "location" <> 'santa_monica' AND (water_level < -0.59 OR water_level > 9.95)
name: h2o_feet
time                water_level
----                -----------
1566976680000000000 9.957
1566977040000000000 9.964
1566977400000000000 9.954
1567002600000000000 -0.61
1567002960000000000 -0.591
1567091880000000000 -0.594
```

Select data that have **specific timestamps**

```
SELECT * FROM "h2o_feet" WHERE time > now() - 7d
```

查询 过去7天内 `h2o_feet`中的数据

## 1.3 The GROUP BY clause

