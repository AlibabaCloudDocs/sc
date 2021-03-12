---
keyword: 创建交互式分析Hologres源表
---

# 交互式分析Hologres源表

本文为您介绍交互式分析Hologres源表DDL定义、WITH参数、代码示例和类型映射。

## 什么是交互式分析Hologres

交互式分析Hologres兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## DDL定义

```
create table hologres_source(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' --该参数可选。
);
```

**说明：**

-   Flink不支持源表中定义计算列。
-   Flink以批模式读取Hologres源表数据，即只扫描一次Hologres全表，扫描结束，消费结束，新到Hologres源表的数据不会被读取。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型|是|固定值为hologres。|
|dbname|数据库名称|是|无。|
|tablename|表名称**说明：** 如果Schema不为Public时，则tableName需要填写为schema.tableName。

|是|无。|
|username|用户名，请填写阿里云账号的AccessKey ID。|是|无。|
|password|密码，请填写阿里云账号的AccessKey Secret。|是|无。|
|endpoint|Hologres端点|是|详情请参见[访问域名](/intl.zh-CN/实例管理/访问域名.md)。|
|field\_delimiter|导出数据时，不同行之间使用的分隔符。**说明：** 只有在bulkread = true时有效。

|否|默认值为"\\u0002"。|

## 代码示例

```
CREATE TEMPORARY TABLE hologres_source (
  name varchar, 
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'field_delimiter'='|' --该参数可选。
);

CREATE TEMPORARY TABLE blackhole_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT 
) with (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink
SELECT 
   name, age, birthday
from hologres_source;
```

## 类型映射

交互式分析Hologres和Flink字段类型对应关系如下，建议使用该对应关系进行DDL声明。

|Hologres字段类型|Flink字段类型|
|------------|---------|
|INT|INT|
|INT\[\]|ARRAY|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY|
|REAL|FLOAT|
|REAL\[\]|ARRAY|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

