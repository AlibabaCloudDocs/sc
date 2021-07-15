---
keyword: [表格存储Tablestore, 结果表]
---

# 表格存储Tablestore结果表

本文为您介绍表格存储Tablestore结果表的DDL定义、WITH参数和映射关系。

## 什么是表格存储Tablestore

[表格存储Tablestore](/cn.zh-CN/产品简介/什么是表格存储.md)是基于阿里云飞天分布式系统的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发的无缝扩展，提供海量结构化数据的存储和实时访问服务。Flink支持将Tablestore作为流式数据的输出。

## 前提条件

已创建Tablestore数据表，详情请参见[创建数据表](/cn.zh-CN/快速入门/创建数据表.md)。

## 使用限制

仅Flink计算引擎VVR 2.0.0及以上版本支持表格存储Tablestore Connector。

## DDL定义

```
CREATE TABLE ots_sink (
  name VARCHAR,
  age BIGINT,
  birthday BIGINT,
  primary key(name,age) not enforced
) WITH (
  'connector'='ots',
  'instanceName'='<yourInstanceName>',
  'tableName'='<yourTableName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessSecret>',
  'endPoint'='<yourEndpoint>',
  'valueColumns'='birthday'
); 
```

**说明：** Tablestore结果表必须定义有`Primary Key`，输出数据以Update方式追加Tablestore表。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型。|是|固定值为`ots`。|
|instanceName|实例名称。|是|无。|
|tableName|表名称。|是|无。|
|endPoint|实例访问地址。|是|请参见[服务地址](/cn.zh-CN/功能介绍/基础概念/服务地址.md)。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|valueColumns|插入字段的列名。|是|多个字段以英文逗号（,）分割，例如`ID,NAME`。|
|bufferSize|流入多少条数据后开始输出。|否|默认值为5000，表示输入的数据达到5000条就开始输出。|
|batchWriteTimeoutMs|写入超时的时间。|否|单位为毫秒，默认值为5000。表示如果缓存中的数据在等待5秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|
|batchSize|一次批量写入的条数。|否|默认值为100。|
|retryIntervalMs|重试间隔时间。|否|单位为毫秒，默认值为1000。|
|maxRetryTimes|最大重试次数。|否|默认值为10。|
|ignoreDelete|是否忽略DELETE操作。|否|默认值为False。|
|connectTimeout|Connector连接Tablestore的超时时间。|否|单位毫秒，默认值为30000（30秒）。|
|socketTimeout|Connector连接Tablestore的Socket超时时间。|否|单位毫秒，默认值为30000（30秒）。|

## 类型映射

|Tablestore字段类型|Flink字段类型|
|--------------|---------|
|INTEGER|BIGINT|
|STRING|STRING|
|BOOLEAN|BOOLEAN|
|DOUBLE|DOUBLE|

