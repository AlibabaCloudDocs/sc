---
keyword: [云数据库RDS MySQL, 云数据库RDS版, MySQL]
---

# 云数据库RDS MySQL结果表

本文为您介绍云数据库RDS MySQL结果表的DDL定义、WITH参数和类型映射。

## 什么是云数据库RDS MySQL

RDS MySQL基于阿里巴巴的MySQL源码分支，经过双十一高并发、大数据量的考验，拥有优良的性能。RDS MySQL支持实例管理、账号管理、数据库管理、备份恢复、白名单、透明数据加密以及数据迁移等基本功能。RDS MySQL详情请参见[概述](/cn.zh-CN/RDS MySQL 数据库/概述.md)。

## 前提条件

-   已创建RDS MySQL数据库和表，详情请参见[创建数据库和账号](/cn.zh-CN/RDS MySQL 数据库/快速入门/创建数据库和账号.md)。
-   已设置IP白名单，详情请参见[设置IP白名单](/cn.zh-CN/RDS MySQL 数据库/快速入门/设置白名单/设置IP白名单.md)。

## 语法示例

```
CREATE TABLE rds_sink (
   id INT,
   num BIGINT,
   PRIMARY KEY (id) NOT ENFORCED
) WITH (
   'connector' = 'rds',
   'password' = '<yourPassword>',
   'tableName' = '<yourTablename>',
   'url' = '<yourUrl>',
   'userName' = '<yourUsername>'
);
```

**说明：**

-   Flink写入RDS或DRDS数据库结果表原理：针对Flink每行结果数据，拼接成一行SQL语句，输入至目标端数据库，然后执行。如果使用批量写，需要在URL后面加上参数`?rewriteBatchedStatements=true`，以提高系统性能。
-   RDS MySQL数据库支持自增主键，因此在DDL中不声明该自增字段。例如ID是自增字段，Flink DDL不声明该自增字段，则数据库在一行数据写入过程中会自动填补相关自增字段。
-   如果DRDS有分区表，拆分键必须在DDL里PRIMARY KEY（）中声明，否则拆分的表无法写入。
-   DDL声明的字段必须至少存在一个非主键的字段，否则产生报错。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型。|是|固定值为`rds`。|
|password|密码。|是|无。|
|tableName|表名。|是|无。|
|url|URL地址。|是|云数据库RDS版专有网络VPC地址，即内网地址，详情请​参见[查看或修改内外网地址和端口](/cn.zh-CN/RDS MySQL 数据库/数据库连接/查看或修改内外网地址和端口.md)。URL的格式为：`jdbc:mysql://<内网地址>/<databaseName>`，其中databaseName为对应的数据库名称。**说明：** 如果使用批量写，需要在URL后面加上参数`?rewriteBatchedStatements=true`，以提高系统性能。 |
|userName|用户名。|是|无。|
|maxRetryTimes|写入数据失败后，重试写入的次数。|否|默认值为3。|
|batchSize|一次批量写入的条数。|否|默认值为5000。|
|flushIntervalMs|清空缓存的时间间隔。|否|单位为毫秒，默认值为 0，即默认不开启缓存flush。如果设置值为2000，表示如果缓存中的数据在等待2秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|

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
CREATE TEMPORARY TABLE datagen_source (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE rds_sink (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'rds',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
);

INSERT INTO rds_sink
SELECT  * FROM datagen_source;
```

## 常见问题

-   [Flink的结果数据写入RDS表，是按主键更新的，还是生成1条新的记录？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [使用RDS表中的唯一索引进行GROUP BY时需要注意什么？](/cn.zh-CN/Flink全托管/常见问题.md)

