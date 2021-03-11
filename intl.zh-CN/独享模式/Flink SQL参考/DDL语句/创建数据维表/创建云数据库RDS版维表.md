---
keyword: [RDS, 维表, cache]
---

# 创建云数据库RDS版维表

本文为您介绍如何创建实时计算Flink版云数据库RDS（DRDS）版维表，以及创建维表时使用的WITH参数、CACHE参数和类型映射。

**说明：** 实时计算Flink版暂不支持通过数据存储功能中存储注册的方式使用RDS MySQL 8.0版本，建议您使用明文方式使用RDS MySQL 8.0版本。数据存储功能详情请参见[数据存储概述](/intl.zh-CN/独享模式/Flink SQL开发指南/数据存储/数据存储概述.md)

## 云数据库RDS版

阿里云关系型数据库（Relational Database Service，RDS）是一种稳定可靠、可弹性伸缩的在线数据库服务。RDS基于阿里云分布式文件系统和高性能存储，支持MySQL、SQL Server、PostgreSQL和PPAS（Postgres Plus Advanced Server）引擎，并且提供了容灾、备份、恢复、监控和迁移等方面的全套解决方案。

**说明：** 云数据库RDS、DRDS和PolarDB版插件中的WITH参数一致，可以通用。在使用云数据库RDS、DRDS和PolarDB作为维表时，RDS、DRDS和PolarDB中必须存在真实的表。

## 语法示例

实时计算Flink版支持使用RDS或DRDS作为维表（目前仅支持MySQL数据存储类型），示例代码如下。

```
CREATE TABLE rds_dim_table(
 id INT,
 len INT,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME--定义维表的变化周期。
) with (
 type='rds',
 url='<yourDatabaseURL>',
 tableName='<yourDatabaseTableName>',
 userName='<yourDatabaseUserName>',
 password='<yourDatabasePassword>'
);
```

**说明：** 声明维表时，必须要指名主键。维表JOIN时，ON条件必须包含所有主键的等值条件。RDS或DRDS的主键可以定义为表的主键或唯一索引列。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|维表类型|是|固定值为`rds`。|
|url|JDBC（Java DataBase Connectivity）连接地址|是|URL的格式为：`jdbc:mysql://<内网地址>/<databaseName>`，其中databaseName为对应的数据库名称。内网地址参见如下链接：-   [Apply for an Internet IP address for RDS](https://www.alibabacloud.com/help/doc-detail/26128.htm) |
|tableName|表名|是|无|
|userName|用户名|是|无|
|password|密码|是|无|
|maxRetryTimes|最大尝试连接次数|否|默认值为10。|

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|云数据库RDS（DRDS）版维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。

-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。

需要配置相关参数：缓存更新时间间隔（cacheTTLMs），更新时间黑名单（cacheReloadTimeBlackList）。


**说明：** 因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小|否|当选择`LRU`缓存策略后，可以设置缓存大小，默认10000行。|
|cacheTTLMs|缓存超时时间，单位为毫秒|否|当选择`LRU`缓存策略后，可以设置缓存失效的超时时间，默认不过期。当选择`ALL`策略，则为缓存加载的间隔时间，默认不重新加载。|
|cacheReloadTimeBlackList|缓存策略选择`ALL` 时启用。更新时间黑名单，防止在此时间内做cache更新（例如双11场景）。|否|默认为空。自定义输入格式为`2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`。用逗号（,）来分隔多个黑名单，用箭头（-\>）来分割黑名单的起始结束时间。|

## 代码示例

包含云数据库RDS版维表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);

create table phoneNumber(
name VARCHAR,
phoneNumber BIGINT,
primary key(name),
PERIOD FOR SYSTEM_TIME--定义维表的变化周期。
)with(
type='rds'
);

CREATE table result_infor(
id BIGINT,
phoneNumber BIGINT,
name VARCHAR
)with(
type='rds'
);

INSERT INTO result_infor
SELECT
t.id,
w.phoneNumber,
t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w --维表JOIN时必须指定此声明。
ON t.name = w.name;
```

维表详细语法请参见[维表JOIN语句](/intl.zh-CN/独享模式/Flink SQL参考/QUERY语句/维表JOIN语句.md)。

## 类型映射

|RDS字段类型|实时计算Flink版字段类型|
|-------|--------------|
|BOOLEAN|BOOLEAN|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|VARBINARY|VARBINARY|

