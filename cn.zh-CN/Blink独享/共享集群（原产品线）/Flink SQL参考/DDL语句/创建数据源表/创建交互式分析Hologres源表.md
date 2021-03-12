---
keyword: 创建交互式分析Hologres源表
---

# 创建交互式分析Hologres源表

本文为您介绍创建交互式分析Hologres源表DDL定义，以及创建源表时用到的WITH参数、类型映射和代码示例。

**说明：**

-   本文仅适用于Blink 3.6.0及以上版本，如果您的Blink为3.6.0以下的版本，您可以[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)[提交工单](https://workorder-intl.console.aliyun.com/)获取需要的JAR文件，安装使用。
-   建议您使用Hologres 0.7及以上版本。
-   仅支持行存储Hologres表作为实时计算Flink源表。
-   Hologres源表支持Projection Pushdown操作，您可以只读取需要的列。
-   并发的Blink作业都可以读取一个或多个Hologres Shard，建议您配置的Blink并发数小于等于Hologres的Shard数。
-   在流数据和批数据处理中都可以使用Hologres源表。
-   Hologres源表使用快照语句高速读取当前数据，读取完后结束作业。如果出现读取数据失败，将重新执行读取操作。

## 什么是交互式分析Hologres

交互式分析Hologres兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## DDL定义

```
create table mysource(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='...',
  tablename='...',
  username='...',
  password='...',
  endpoint='...',
  field_delimiter='...' --该参数可选。
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|源表类型|是|固定值为hologres。|
|dbname|数据库名称|是|无|
|tablename|表名称**说明：** 如果Schema不为Public时，则tableName需要填写为schema.tableName。

|是|无|
|username|用户名，请填写阿里云账号的AccessKey ID。|是|无|
|password|密码，请填写阿里云账号的AccessKey Secret。|是|无|
|endpoint|Hologres端点|是|详情请参见[访问域名](/cn.zh-CN/实例管理/访问域名.md)。请联系阿里云运维工程师获取。|
|field\_delimiter|导出数据时，不同行之间使用的分隔符。 **说明：** 不能在数据中插入分隔符。

|是|默认值为"\\u0002"。|

## 类型映射

|Hologres|BLINK|
|--------|-----|
|INT|INT|
|INT\[\]|ARRAY<INT\>|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY<BIGINT\>|
|REAL|FLOAT|
|REAL\[\]|ARRAY<FLOAT\>|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY<DOUBLE\>|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY<BOOLEAN\>|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY<VARCHAR\>|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

## 代码示例

包含Hologres源表的实时计算作业代码示例如下。

```
create table mysource(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='...',
  tablename='...',
  username='...',
  password='...',
  endpoint='...',
  field_delimiter='...' --该参数可选。
);


create table print_output(
 a varchar,
 b BIGINT,
 c BIGINT 
) with (
  type='print'
);


INSERT INTO print_output
SELECT 
  a, b, c
from mysource;
```

