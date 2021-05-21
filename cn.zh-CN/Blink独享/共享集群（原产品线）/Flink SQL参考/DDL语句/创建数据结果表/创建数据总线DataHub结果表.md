# 创建数据总线DataHub结果表

本文为您介绍如何创建实时计算数据总线DataHub结果表，以及创建结果表时使用的WITH参数和类型映射。

**说明：** 本文仅适用于Blink 1.4.5及以上版本。

## 什么是数据总线DataHub

阿里云流数据处理平台DataHub是流式数据（Streaming Data）的处理平台，提供对流式数据的发布（Publish）、订阅（Subscribe）和分发功能，让您可以轻松构建基于流式数据的分析和应用。实时计算Flink版使用DataHub作为流式数据存储源头或输出目的端。

## DDL定义

实时计算支持使用DataHub作为数据输出结果表，DataHub结果表声明示例如下。

```
create table datahub_output(
  name VARCHAR,
  age BIGINT,
  birthday BIGINT
)with(
  type='datahub',
  endPoint='<yourEndpoint>,
  project='<yourProjectName>',
  topic='<yourTopicName>',
  accessId='<yourAccessId>',
  accessKey='<yourAccessKey>',
  batchSize='<yourBatchSize>',
  batchWriteTimeoutMs='1000'
);
```

**说明：** 建议您使用存储注册功能，详情请参见[注册数据总线DataHub](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/注册数据存储/注册数据总线DataHub.md)。

## WITH参数

|参数|参数说明|是否必填|备注|
|--|----|----|--|
|type|源表类型|是|固定值为`datahub`。|
|endPoint|endPoint地址|是|DataHub的endPoint地址详情，请参见[DataHub域名列表](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC)。|
|project|DataHub项目名称|是|无|
|topic|DataHub中Topic名称|是|无|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|maxRetryTimes|读取最大重试次数|否|不同版本中，该参数的默认值如下： -   Blink 2.2.7以下版本：3
-   Blink 2.2.7及以上版本：20 |
|batchSize|一次批量写入的条数|否|不同版本中，该参数的默认值如下： -   Blink 3.3以下版本：300
-   Blink 3.3及以上版本：100 |
|batchWriteTimeoutMs|缓存数据的超时时间|否|可选，单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|maxBlockMessages|每次写入的最大Block数|否|默认值为100。|
|reserveMilliSecond|TIMESTAMP类型是否保留毫秒|否|默认值为false。**说明：** 仅实时计算2.2.6以上版本支持该功能。 |
|partitionBy|写入结果表前会根据该值进行Hash分类，数据会流向对应的结果表。|否|默认值为空，随机进行数据分配。 **说明：** partitionBy决定数据流到Blink的哪个Subtask。 |
|hashFields|指定了列名之后，相同列的值会写入到同一个Shard。|否|默认值为Null，即随机写入。可以指明多个列值，用逗号（,）分隔。例如，`hashFields='a,b'`。 **说明：**

-   仅Blink 3.3.0及以上版本支持该功能。
-   hashFields决定数据流写到DataHub的哪个Shard。 |

## 类型映射

DataHub和实时计算字段类型对应关系如下，建议使用该对应关系时进行DDL声明。

|DataHub字段类型|实时计算字段类型|
|-----------|--------|
|BIGINT|BIGINT|
|TIMESTAMP|BIGINT **说明：** DataHub的TIMESTAMP精确到微秒，在Unix时间戳中为16位，但实时计算定义的TIMESTAMP精确到毫秒，在Unix时间戳中为13位，所以建议您使用BIGINT进行映射。

如果您需要使用TIMESTAMP，建议使用计算列进行转换，详情请参见[计算列](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/基本概念/计算列.md)。 |
|STRING|VARCHAR|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DECIMAL|DECIMAL|

## 代码示例

包含DataHub结果表的实时计算作业代码示例如下。

```
create table datahub_input(
  name VARCHAR
) with (
  type='datahub',
  endPoint='http://dh-cn-hangzhou.aliyun-inc.com',
  project='test1',
  topic='topic1',
  accessId='<yourAccessID>',
  accessKey='<yourAccessSecret>',
  startTime='2018-06-01 00:00:00'
);

create table datahub_output(
  name varchar
)with(
  type='datahub',
  endPoint='http://dh-cn-hangzhou.aliyun-inc.com',
  project='test2',
  topic='topic2',
  accessId='<yourAccessID>',
  accessKey='<yourAccessSecret>',
  batchSize='1000',
  batchWriteTimeoutMs='500'
);

INSERT INTO datahub_output
SELECT 
  LOWER(name)
from datahub_input;
```

