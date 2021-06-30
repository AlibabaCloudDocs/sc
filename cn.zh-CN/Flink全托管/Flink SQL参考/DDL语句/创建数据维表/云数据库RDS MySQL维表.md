---
keyword: [云数据库RDS MySQL, 云数据库RDS版, MySQL]
---

# 云数据库RDS MySQL维表

本文为您介绍云数据库RDS MySQL维表DDL定义、WITH参数、CACHE参数、类型映射和代码示例。

## 什么是云数据库RDS MySQL

RDS MySQL基于阿里巴巴的MySQL源码分支，经过双十一高并发、大数据量的考验，拥有优良的性能。RDS MySQL支持实例管理、账号管理、数据库管理、备份恢复、白名单、透明数据加密以及数据迁移等基本功能。RDS MySQL详情请参见[概述](/cn.zh-CN/RDS MySQL 数据库/概述.md)。

## 前提条件

-   已创建RDS MySQL数据库和表，详情请参见[创建数据库和账号](/cn.zh-CN/RDS MySQL 数据库/快速入门/创建数据库和账号.md)。
-   已设置IP白名单，详情请参见[设置IP白名单](/cn.zh-CN/RDS MySQL 数据库/快速入门/设置白名单/设置IP白名单.md)。

## 语法示例

```
CREATE TABLE rds_dim (
  id1 INT,
  id2 VARCHAR
) WITH (
  'connector' = 'rds',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>',
  'cache' = 'ALL'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型|是|固定值为`rds`。|
|password|密码|是|无|
|tableName|表名|是|无|
|url|URL地址|是|云数据库RDS版专有网络VPC地址，即内网地址，详情请​参见[查看或修改内外网地址和端口](/cn.zh-CN/RDS MySQL 数据库/数据库连接/查看或修改内外网地址和端口.md)。URL的格式为：`jdbc:mysql://<内网地址>/<databaseName>`，其中databaseName为对应的数据库名称。|
|userName|用户名|是|无|
|maxRetryTimes|写入数据失败后，重试写入的次数|否|默认值为3。|
|CACHE|缓存策略、缓存大小和缓存超时时间|否|详情请参见[CACHE参数](#section_5g6_dkf_nd2)。|

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|云数据库RDS（DRDS）版维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。
-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。


**说明：**

-   使用ALL或LRU缓存策略时，必须配置cacheSize参数。
-   如果使用CACHE ALL时，请注意节点内存大小，防止出现OOM。
-   因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小|否|-   当选择LRU缓存策略后，必须设置缓存大小。
-   当选择ALL缓存策略后，可以不设置缓存大小。 |
|cacheTTLMs|缓存超时时间，单位为毫秒|否|当选择`LRU`缓存策略后，可以设置缓存失效的超时时间。当选择`ALL`策略，则为缓存加载的间隔时间，默认值为10s。|

## 类型映射

|RDS字段类型|Flink字段类型|
|-------|---------|
|BOOLEAN|BOOLEAN|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|TINYINT UNSIGNED|SMALLINT|
|INT|INT|
|SMALLINT UNSIGNED|INT|
|BIGINT|BIGINT|
|INT UNSIGNED|BIGINT|
|BIGINT UNSIGNED|DECIMAL\(20,0\)|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|VARBINARY|VARBINARY|

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source(
  a INT,
  b BIGINT,
  c STRING,
  `proctime` AS PROCTIME()
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE rds_dim (
  a INT, 
  b VARCHAR, 
  c VARCHAR
) with (
  'connector' = 'rds',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = 'jdbc:mysql://xxx',
  'userName' = '<yourUsername>'
);

CREATE TEMPORARY TABLE blackhole_sink(
  a INT,
  b STRING
) with (
  'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN rds_dim FOR SYSTEM_TIME AS OF T.`proctime` AS H ON T.a = H.a;
```

