---
keyword: [DataHub, 源表]
---

# 数据总线DataHub源表

本文为您介绍数据总线DataHub源表的DDL定义、WITH参数、类型映射、属性字段和常见问题。

## 什么是数据总线DataHub

[阿里云流数据处理平台DataHub](https://help.aliyun.com/document_detail/47439.html?spm=a2c4g.11174283.6.542.5e7b63efe2IHMj)是流式数据（Streaming Data）的处理平台，提供对流式数据的发布（Publish）、订阅（Subscribe）和分发功能，让您可以轻松构建基于流式数据的分析和应用。

## 前提条件

已创建DataHub的Project和Topic，详情请参见[创建Project和Topic]()。

## DDL定义

```
create table datahub_source(
  name VARCHAR,
  age BIGINT,
  birthday BIGINT
) with (
  'connector' = 'datahub',
  'endPoint' = '<yourEndPoint>',
  'project' = '<yourProjectName>',
  'topic' = '<yourTopicName>',
  'subId' = '<yourSubId>',
  'accessId' = '<yourAccessId>',
  'accessKey' = '<yourAccessKey>'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型。|是|固定值为`datahub`。|
|endPoint|消费端点信息。|是|详情请参见[DataHub域名列表](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC)。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|project|项目名称。|是|无。|
|topic|Topic名称。|是|无。|
|subId|Topic的订阅ID。|是|多个任务不能同时使用同一个订阅。|
|startTime|启动位点的时间。|否|格式为`yyyy-MM-dd hh:mm:ss`。|
|retryTimeout|最大持续重试时间。|否|单位为毫秒，默认值为1800000毫秒（半小时）。|
|retryInterval|重试间隔。|否|单位为毫秒，默认值为1000。|
|maxFetchSize|单次读取条数。|否|默认值为50。|
|maxBufferSize|异步读取的最大缓存数据条数。|否|默认值为50。|
|lengthCheck|单行字段条数检查策略。|否|-   NONE（默认值）：
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，跳过该行数据。
-   SKIP：解析出的字段数和定义字段数不同时跳过该行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，按从左到右的顺序，在行尾用Null填充缺少的字段。 |
|columnErrorDebug|是否打开调试开关。|否|-   false（默认值）：关闭调试功能。
-   true：打开调试开关，打印解析异常的日志。 |

## 类型映射

DataHub和Flink字段类型对应关系如下，建议使用该对应关系进行DDL声明。

|DataHub字段类型|Flink字段类型|
|-----------|---------|
|STRING|VARCHAR|
|TIMESTAMP|BIGINT|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INTEGER|INTEGER|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DECIMAL|DECIMAL|

**说明：** 如果您DataHub中存储的是二进制BLOB类型数据时，则Flink字段需要声明为VARBINARY类型，与METAQ类似。

## 属性字段

|字段名|字段类型|说明|
|---|----|--|
|shard-id|BIGINT METADATA VIRTUAL|Shard的ID。|
|sequence|STRING METADATA VIRTUAL|数据顺序。|
|system-time|TIMESTAMP METADATA VIRTUAL|系统时间。|

**说明：** 仅在VVR 3.0.1及以上版本支持获取以上DataHub属性字段。

## 代码示例

```
CREATE TEMPORARY TABLE datahub_input (
  `time` BIGINT,
  `sequence`  STRING METADATA VIRTUAL,
  `shard-id` BIGINT METADATA VIRTUAL,
  `system-time` TIMESTAMP METADATA VIRTUAL
) WITH (
  'connector' = 'datahub',
  'subId' = '<yourSubId>',
  'endPoint' = '<yourEndPoint>',
  'project' = '<yourProjectName>',
  'topic' = '<yourTopicName>',
  'accessid' = '<yourAccessId>',
  'accesskey' = '<yourAccessKey>'
);

CREATE TEMPORARY TABLE test_out (
  `time` BIGINT,
  `sequence`  STRING,
  `shard-id` BIGINT,
  `system-time` TIMESTAMP
) WITH (
  'connector' = 'print',
  'logger' = 'true'
);

INSERT INTO test_out
SELECT 
  `time`,
  `sequence` ,
  `shard-id`,
  `system-time`
FROM datahub_input;
```

## 常见问题

-   [分裂或者缩容DataHub Topic后导致Flink作业失败，如何恢复？](/cn.zh-CN/Flink全托管/常见问题.md)
-   [可以删除正在消费的DataHub Topic吗？](/cn.zh-CN/Flink全托管/常见问题.md)

