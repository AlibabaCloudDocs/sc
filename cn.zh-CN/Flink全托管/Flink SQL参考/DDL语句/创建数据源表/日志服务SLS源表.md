---
keyword: [日志服务, 源表]
---

# 日志服务SLS源表

本文为您介绍日志服务SLS源表的DDL定义、WITH参数、类型映射和属性字段。

## 什么是日志服务

[日志服务SLS](/cn.zh-CN/产品简介/什么是日志服务.md)是针对日志类数据的一站式服务。日志服务可以帮助您快捷地完成数据采集、消费、投递以及查询分析，提升运维和运营效率，建立海量日志处理能力。

## DDL定义

```
create table sls_source(
  a int,
  b int,
  c varchar
) with (
  'connector' ='sls',  
  'endPoint' ='<endPoint>',
  'accessId' ='<yourAccessId>',
  'accessKey' ='<yourAccessKey>',
  'startTime' = '2017-07-05 00:00:00',
  'project' ='<yourProjectName>',
  'logStore' ='<yourLogStoreName>',
  'consumerGroup' ='<yourConsumerGroupName>'
);
```

**说明：**

-   SLS暂不支持MAP类型的数据。
-   SLS对于不存在字段会置为Null。
-   字段顺序可以为无序（建议字段顺序和表中定义一致）。

## WITH参数

|参数|说明|是否必选|备注|
|--|--|----|--|
|connector|源表类型|是|固定值为`sls`。|
|endPoint|消费端点信息|是|[服务入口](/cn.zh-CN/开发指南/API 参考/服务入口.md)|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|project|读取的SLS项目名称|是|无|
|logStore|Project下的具体的LogStore名称|是|无|
|startTime|日志消费的开始时间|是|无|
|consumerGroup|消费组名|否|您可以自定义消费组名（没有固定格式）。|
|batchGetSize|单次读取logGroup的条数|否|默认值为10。**说明：**

-   batchGetSize设置不能超过1000，否则会报错。
-   batchGetSize设置单次读取logGroup的条数。如果单条logItem的大小和batchGetSize都很大，则可能会导致频繁的垃圾回收（Garbage Collection），您可以适当减小batchGetSize参数值。 |

## 类型映射

日志服务和Flink字段类型对应关系如下。建议您使用该对应关系进行DDL声明。

|日志服务字段类型|Flink字段类型|
|--------|---------|
|STRING|VARCHAR|

## 属性字段

Flink暂不支持获取SLS属性字段，例如如下字段等。

|字段名|说明|
|---|--|
|`__source__`|消息源|
|`__topic__`|消息主题|
|`__timestamp__`|日志时间|

