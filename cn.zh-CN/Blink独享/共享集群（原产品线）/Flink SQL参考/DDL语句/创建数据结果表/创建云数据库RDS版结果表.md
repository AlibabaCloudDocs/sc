# 创建云数据库RDS版结果表

本文为您介绍如何创建实时计算Flink版云数据库RDS（DRDS）版结果表，以及创建结果表时使用的WITH参数和类型映射。

**说明：** 实时计算Flink版暂不支持通过数据存储功能中存储注册的方式使用RDS MySQL 8.0版本，建议您使用明文方式使用RDS MySQL 8.0版本。数据存储功能详情请参见[数据存储概述](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/数据存储概述.md)

## 什么是云数据库RDS版

阿里云关系型数据库（Relational Database Service，RDS）是一种稳定可靠、可弹性伸缩的在线数据库服务。RDS基于阿里云分布式文件系统和高性能存储，支持MySQL、SQL Server、PostgreSQL和PPAS（Postgres Plus Advanced Server）引擎，并且提供了容灾、备份、恢复、监控和迁移等方面的全套解决方案。

**说明：** 云数据库RDS、DRDS和PolarDB版插件中的WITH参数一致，可以通用。在使用云数据库RDS、DRDS和PolarDB作为结果表时，RDS、DRDS和PolarDB中必须存在真实的表。

## 语法示例

实时计算Flink版支持使用RDS或DRDS作为结果输出（目前仅支持MySQL数据存储类型），示例代码如下。

```
CREATE TABLE rds_output(
   id INT,
   len INT,
   content VARCHAR,
   PRIMARY KEY (id,len)
) WITH (
   type='rds',
   url='<yourDatabaseURL>',
   tableName='<yourDatabaseTable>',
   userName='<yourDatabaseUserName>',
   password='<yourDatabasePassword>'
);        
```

**说明：**

-   实时计算Flink版写入RDS或DRDS数据库结果表原理：针对实时计算Flink版每行结果数据，拼接成一行SQL语句，输入至目标端数据库，然后执行。如果使用批量写，需要在URL后面加上参数`?rewriteBatchedStatements=true`，以提高系统性能。
-   RDS MySQL数据库支持自增主键。如果实时计算Flink版写入数据支持自增主键，则在DDL中不声明该自增字段即可。例如，ID是自增字段，实时计算Flink版DDL不声明该自增字段，则数据库在一行数据写入过程中会自动填补相关自增字段。
-   如果DRDS有分区表，拆分键必须在实时计算Flink版DDL里PRIMARY KEY（）中声明，否则拆分的表无法写入。
-   建议使用数据存储注册方式，请参见[注册云数据库RDS版](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/注册数据存储/注册云数据库RDS版.md)。
-   DDL声明的字段必须至少存在一个非主键的字段，否则产生报错。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型|是|固定值为`rds`。|
|url|JDBC（Java DataBase Connectivity）连接地址|是|URL的格式为：`jdbc:mysql://<内网地址>/<databaseName>`，其中databaseName为对应的数据库名称。内网地址参见如下链接：-   [RDS的内网地址](https://help.aliyun.com/document_detail/26128.html?spm=5176.doc43185.6.581.rxQuNz)
-   [DRDS的内网地址](https://help.aliyun.com/document_detail/56494.html)

**说明：** 如果访问通过VPC访问授权过的RDS，对应的URL配置请参见[VPC访问授权](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/VPC访问授权.md)。 |
|tableName|表名|是|无|
|userName|用户名|是|无|
|password|密码|是|无|
|maxRetryTimes|最大重试次数|否|默认值为10。|
|batchSize|一次批量写入的条数|否|默认值为4096。|
|bufferSize|流入多少条数据后开始去重|否|默认值为10000。|
|flushIntervalMs|清空缓存的时间间隔|否|默认值为2000，单位为毫秒。表示如果缓存中的数据在等待2秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|excludeUpdateColumns|忽略指定字段的更新|否|默认值为空（默认忽略PRIMARY KEY字段）。表示更新主键值相同的数据时，忽略指定字段的更新。|
|ignoreDelete|是否忽略delete操作|否|默认值为false，表示支持delete功能。|
|partitionBy|分区|否|默认为空。表示写入Sink节点前，会根据该值进行Hash分区，数据会流向相应的Hash节点。|

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

## JDBC连接参数

|参数名称|说明|默认值|最低版本要求|
|----|--|---|------|
|useUnicode|是否使用Unicode字符集，如果参数characterEncoding设置为GB2312或GBK，本参数值必须设置为true。|false|1.1g|
|characterEncoding|当useUnicode设置为true时，指定字符编码。例如可设置为GB2312或GBK。|false|1.1g|
|autoReconnect|当数据库连接异常中断时，是否自动重新连接。|false|1.1|
|autoReconnectForPools|是否使用针对数据库连接池的重连策略。|false|3.1.3|
|failOverReadOnly|自动重连成功后，连接是否设置为只读。|true|3.0.12|
|maxReconnects|autoReconnect设置为true时，重试连接的次数。|3|1.1|
|initialTimeout|autoReconnect设置为true时，两次重连之间的时间间隔，单位为秒。|2|1.1|
|connectTimeout|和数据库服务器建立socket连接时的连接超时时长，单位为毫秒。0表示永不超时，适用于JDK 1.4及以上版本。|0|3.0.1|
|socketTimeout|socket操作（读写）超时，单位：毫秒。0表示永不超时。|0|3.0.1|

## 代码示例

包含云数据库RDS版结果表的实时计算Flink版作业代码示例如下。

```
CREATE TABLE source (
   id INT,
   len INT,
   content VARCHAR
) with (
   type = 'random'
);

CREATE TABLE rds_output(
   id INT,
   len INT,
   content VARCHAR,
   PRIMARY KEY (id,len)
) WITH (
   type='rds',
   url='<yourDatabaseURL>',
   tableName='<yourDatabaseTable>',
   userName='<yourDatabaseUserName>',
   password='<yourDatabasePassword>'
);

INSERT INTO rds_output
SELECT id, len, content FROM source;
```

## FAQ

-   Q：实时计算Flink版的结果数据写入RDS表，是按主键更新的，还是生成1条新的记录？

    A：如果在DDL中定义了主键，会采用`INSERT INTO tablename(field1,field2, field3, ...) VALUES(value1, value2, value3, ...) ON DUPLICATE KEY UPDATE field1=value1,field2=value2, field3=value3, ...;`的方式更新记录，即对于不存在的主键字段会直接插入，存在的主键字段则更新相应的值。如果DDL中没有声明PRIMARY KEY，则会用`insert into`方式插入记录，追加数据。

-   Q：使用RDS表中的唯一索引进行GROUP BY时需要注意什么？

    A：

    -   需要在作业中的GROUP BY中声明该唯一索引。
    -   RDS中只有一个自增主键，实时计算Flink版作业中不能声明为PRIMARY KEY。

