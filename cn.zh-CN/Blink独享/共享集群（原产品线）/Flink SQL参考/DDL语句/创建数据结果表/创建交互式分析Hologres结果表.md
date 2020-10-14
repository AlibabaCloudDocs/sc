---
keyword: 创建交互式分析Hologres结果表
---

# 创建交互式分析Hologres结果表

本文为您介绍如何创建交互式分析Hologres结果表，以及创建结果表时使用的WITH参数、流式语义和类型映射。

**说明：**

-   本文仅适用于Blink 3.6.0及以上版本正式，如果您的Blink为3.6.0以下的版本，您可以：
    -   升级Blink版本至3.6.0及以上版本，详细请参见[管理独享集群Blink版本](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/管理独享集群Blink版本.md)。
    -   [提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?spm=5176.2020520129.console-base-top.dwork-order-1.29d546aee0gsiH)获取需要的JAR文件，安装使用。
-   建议您使用Hologres 0.7及以上版本。
-   由于Hologres是异步写入数据的，因此需要添加blink.checkpoint.fail\_on\_checkpoint\_error=true作业参数，作业异常时才会触发Failover。

## 什么是交互式分析Hologres

交互式分析Hologres兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## 语法示例

实时计算Flink版支持使用Hologres作为结果表，代码示例如下。

```
create table Hologres_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  type='hologres',
  dbname='<yourDbname>',
  tablename='<yourTablename>',
  username='<yourUsername>',
  password='<yourPassword>',
  endpoint='<yourEndpoint>',
  field_delimiter='|' --该参数可选。
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型|是|固定值为hologres。|
|dbname|数据库名称|是|无|
|tablename|表名称|是|无|
|username|用户名|是|无|
|password|密码|是|无|
|endpoint|Hologres VPC 端点信息|是|详情请参见[访问域名](/cn.zh-CN/实例管理/访问域名.md)。|
|field\_delimiter|导出数据时，不同行之间使用的分隔符。 **说明：** 不能在数据中插入分隔符，且需要与bulkload语义一同使用。

|否|默认值为"\\u0002"。|
|mutationType|流式写入语义，详情请参见[流式语义](#section_yce_507_nhr)。|否|默认值为insertorignore。|
|partitionrouter|分区表写入|否|默认值为false。|
|ignoredelete|是否忽略撤回消息。|否|默认值为false。**说明：** 仅在使用流式语义时生效。 |

## 流式语义

流处理，也称为流数据或流事件处理，即对一系列无界数据或事件连续处理。执行流数据或流事件处理的系统通常允许您指定一种可靠性模式或处理语义，保证整个系统处理数据的准确性，因为网络或设备故障等可能会导致数据丢失。

根据Hologres Sink的配置和Hologres表的属性，流式语义分为以下两种：

-   Exactly-once（仅一次）：即使在发生各种故障的情况下，系统只处理一次数据或事件。
-   At-least-once（至少一次）：如果在系统完全处理之前丢失了数据或事件，则从源头重新传输，因此可以多次处理数据或事件。如果第一次重试成功，则不必进行后续重试。

在Hologres结果表中使用流式语义，您需要注意以下几点：

-   如果Hologres物理表未设置主键，则Hologres Sink使用At-least-once语义。
-   如果Hologres物理表已设置主键，则Hologres Sink通过主键确保Exactly-once语义。当同主键数据出现多次时，您需要设置mutationType参数确定更新结果表的方式，mutationType取值如下：

    -   insertorignore（默认值）：保留首次出现的数据，忽略后续所有数据。
    -   insertorreplace：使用后续出现的数据整行替换已有数据。
    -   insertorupdate：使用后续出现的数据选择性替换已有数据。
    **说明：**

    -   当mutationType设置为insertorupdate或insertorreplace时，系统根据主键更新数据。
    -   Blink定义的结果表中的数据列数不一定要和Hologres物理表的列数一致，您需要保证缺失的列没有非空约束，即列值可以为Null，否则会报错。
-   默认情况下，Hologres Sink只能向一张表导入数据。如果导入数据至分区表的父表，即使导入成功，也会查询数据失败。您可以设置参数partitionRouter为true，开启自动将数据路由到对应分区表的功能。注意事项如下：
    -   tablename参数需要填写为父表的表名。
    -   Blink Connector不会自动创建分区表，因此，需要您提前手动创建需要导入数据的分区表，否则会导入失败。

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

