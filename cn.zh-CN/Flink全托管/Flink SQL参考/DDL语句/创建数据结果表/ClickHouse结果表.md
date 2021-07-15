---
keyword: [创建ClickHouse结果表, ClickHouse]
---

# ClickHouse结果表

本文为您介绍ClickHouse结果表的DDL定义，以及创建结果表时使用的WITH参数、类型映射和代码示例。

## 什么是ClickHouse

ClickHouse是一个用于联机分析（OLAP）的列式数据库管理系统，详情请参见[什么是ClickHouse](https://clickhouse.tech/docs/zh/)。

## 前提条件

-   已创建ClickHouse表，详情请参见[创建表](https://clickhouse.tech/docs/zh/getting-started/tutorial/#create-tables)
-   已配置白名单。
    -   如果您使用的是阿里云数据库ClickHouse，配置白名单详情请参见[设置白名单]()。
    -   如果您使用的是阿里云E-MapReduce的ClickHouse，配置白名单详情请参见[管理安全组](/cn.zh-CN/集群管理/集群配置/管理安全组.md)。
    -   如果您使用的是阿里云ES上自建的ClickHouse，配置白名单详情请参见[安全组概述](/cn.zh-CN/安全/安全组/安全组概述.md)。
    -   如果为其他情况，请您自行配置ClickHouse所在机器的白名单让其可被Flink所在机器访问即可。

## 使用限制

仅Flink计算引擎VVR 3.0.2及以上版本支持ClickHouse Connector。

## DDL定义

```
CREATE TABLE clickhouse_sink (
  id INT,
  name VARCHAR,
  age BIGINT,
  rate FLOAT
) WITH (
  'connector' = 'clickhouse',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'maxRetryTimes' = '3',
  'batchSize' = '8000',
  'flushIntervalMs' = '1000'
)
```

## WITH参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为clickhouse。|
|url|ClickHouse的JDBC连接地址|是|URL格式为`jdbc:clickhouse://<yourNetworAddress>:<PortId>/<yourDatabaseName>`如果不写数据库名称，则使用默认的default数据库。 |
|userName|ClickHouse的用户名|是|无。|
|password|ClickHouse的密码|是|无。|
|tableName|ClickHouse的表名称|是|无。|
|maxRetryTimes|向结果表插入数据失败后的最大尝试次数|否|默认值为3。|
|batchSize|一次批量写入的数据条数|否|默认值为100。|
|flushIntervalMs|清空缓存的时间间隔|否|默认值为1000，单位为毫秒。表示如果缓存中的数据在等待1秒后，数据的条数依然没达到batchSize的数量，系统会自动将缓存中的数据写入ClickHouse表中。|

## 类型映射

|ClickHouse字段类型|Flink字段类型|
|--------------|---------|
|UInt8|BOOLEN|
|Int8|TINYINT|
|Int16|SMALLINT|
|Int32|INTEGER|
|Int64|BIGINT|
|Float32|FLOAT|
|Float64|DOUBLE|
|FixedString|CHAR|
|String|VARCHA|
|FixedString|BINARY|
|String|VARBINARY|
|Date|DATE|
|DateTime|TIMESTAMP\(0\)|
|Datetime64\(x\)|TIMESTAMP\(x\)|
|Decimal|DECIMAL|
|Array|ARRAY|
|不支持|TIME|
|不支持|MAP|
|不支持|MULTISET|
|不支持|ROW|

**说明：** ClickHouse的DateTime类型可以精确到秒，Datetime64可以精确到纳秒。因为ClickHouse官方提供的JDBC写Datetime64数据类型会出现精度丢失，只能精确到秒的问题。所以目前通过Flink只能写入秒级别的TIMESTAMP，即TIMESTAMP\(0\)。

## 代码示例

```
CREATE TEMPORARY TABLE clickhouse_source (
  id INT,
  name VARCHAR,
  age BIGINT,
  rate FLOAT
) WITH (
  'connector' = 'datagen',
  'rows-per-second' = '50'
);

CREATE TEMPORARY TABLE clickhouse_output (
  id INT,
  name VARCHAR,
  age BIGINT,
  rate FLOAT
) WITH (
  'connector' = 'clickhouse',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
);

INSERT INTO clickhouse_output
SELECT 
  id,
  name,
  age,
  rate
FROM clickhouse_source;
```

## 常见问题

[ClickHouse结果表是否支持回撤更新数据？](/cn.zh-CN/Flink全托管/常见问题.md)

