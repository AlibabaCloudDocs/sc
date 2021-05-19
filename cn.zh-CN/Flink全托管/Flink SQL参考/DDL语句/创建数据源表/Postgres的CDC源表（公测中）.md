---
keyword: [Postgres的CDC, Change Data Capture, CDC]
---

# Postgres的CDC源表（公测中）

本文为您介绍Postgres的CDC（Change Data Capture）源表DDL定义、WITH参数、类型映射和代码示例。

**说明：** Postgres的CDC源表正处于公测中，如果您对作业稳定性要求较高时，建议不要使用Postgres的CDC源表。

## 什么是Postgres的CDC源表

Postgres的CDC源表，即Postgres的流式源表，用于依次读取PostgreSQL数据库全量快照数据和变更数据，保证不多读一条也不少读一条数据。即使发生故障，也能采用Exactly Once方式处理。

**说明：** 如果您使用的是开源的PostgreSQL数据库，Postgres CDC connector支持读取的Postgres版本为9.6及以上版本。

## DDL定义

```
CREATE TABLE postgrescdc_source (
  shipment_id INT,
  order_id INT,
  origin STRING,
  destination STRING,
  is_arrived BOOLEAN
) WITH (
  'connector' = 'postgres-cdc',
  'hostname' = '<yourHostname>',
  'port' = '5432',
  'username' = '<yourUserName>',
  'password' = '<yourPassWord>',
  'database-name' = '<yourDatabaseName>',
  'schema-name' = '<yourSchemaName>',
  'table-name' = '<yourTableName>'
);
```

## WITH参数

|参数|说明|是否必填|数据类型|备注|
|--|--|----|----|--|
|connector|源表类型|是|STRING|固定值为`postgres-cdc`。|
|hostname|Postgres数据库的IP地址或者Hostname。|是|STRING|无|
|username|Postgres数据库服务的用户名。|是|STRING|无|
|password|Postgres数据库服务的密码|是|STRING|无|
|database-name|数据库名称|是|STRING|数据库名称支持正则表达式以读取多个数据库的数据。|
|schema-name|Postgres Schema名称|是|STRING|Schema名称支持正则表达式以读取多个Schema的数据。|
|table-name|Postgres表名|是|STRING|表名支持正则表达式去读取多个表的数据。|
|port|Postgres数据库服务的端口号|否|INTEGER|默认值为5432。|
|decoding.plugin.name|Postgres Logical Decoding插件名称|否|STRING|根据Postgres服务上安装的插件确定。支持的插件列表如下： -   decoderbufs（默认值）
-   wal2json
-   wal2json\_rds
-   wal2json\_streaming
-   wal2json\_rds\_streaming
-   pgoutput

**说明：** 如果您使用的是阿里云RDS PostgreSQL，你需要开启逻辑解码（wal2json）功能，详情请参见[逻辑解码（wal2json）](/cn.zh-CN/RDS PostgreSQL 数据库/自研内核 AliPG/逻辑解码（wal2json）.md)。 |
|debezium.\*|Debezium属性参数|否|STRING|更细粒度控制Debezium客户端的行为。例如`'debezium.snapshot.mode' = 'never'`，详情请参见[配置属性](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-connector-properties)。**说明：** 建议每个表都设置`debezium.slot.name`参数，以避免出现`PSQLException: ERROR: replication slot "debezium" is active for PID 974`报错。 |

## 类型映射

Postgres CDC和Flink字段类型对应关系如下。

|Postgres CDC字段类型|Flink字段类型|
|----------------|---------|
|SMALLINT|SMALLINT|
|INT2|
|SMALLSERIAL|
|SERIAL2|
|INTEGER|INT|
|SERIAL|
|BIGINT|BIGINT|
|BIGSERIAL|
|BIGINT|BIGINT|
|REAL|FLOAT|
|FLOAT4|
|FLOAT8|DOUBLE|
|DOUBLE PRECISION|
|NUMERIC\(p, s\)|DECIMAL\(p, s\)|
|DECIMAL\(p, s\)|
|BOOLEAN|BOOLEAN|
|DATE|DATE|
|TIME \[\(p\)\] \[WITHOUT TIMEZONE\]|TIME \[\(p\)\] \[WITHOUT TIMEZONE\]|
|TIMESTAMP \[\(p\)\] \[WITHOUT TIMEZONE\]|TIMESTAMP \[\(p\)\] \[WITHOUT TIMEZONE\]|
|CHAR\(n\)|STRING|
|CHARACTER\(n\)|
|VARCHAR\(n\)|
|CHARACTER VARYING\(n\)|
|TEXT|
|BYTEA|BYTES|

