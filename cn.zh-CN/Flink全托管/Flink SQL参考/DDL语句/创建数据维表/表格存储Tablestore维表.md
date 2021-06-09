---
keyword: [表格存储Tablestore, 维表]
---

# 表格存储Tablestore维表

本文为您介绍表格存储Tablestore维表DDL定义、WITH参数、CACHE参数和常见问题。

## 什么是表格存储Tablestore

[表格存储Tablestore](/cn.zh-CN/产品简介/什么是表格存储.md)是构建在阿里云飞天分布式系统之上的分布式NoSQL数据存储服务。表格存储通过数据分片和负载均衡技术，实现数据规模与访问并发的无缝扩展，提供海量结构化数据的存储和实时访问服务。

## 前提条件

已开通表格存储Tablestore，详情请参见[开通表格存储服务](/cn.zh-CN/快速入门/开通表格存储服务.md)。

## DDL定义

```
CREATE TABLE ots_dim (
  id int,
  len int,
  content STRING
) WITH (
  'connector'='ots',
  'endPoint'='<yourEndpoint>',
  'instanceName'='<yourInstanceName>',
  'tableName'='<yourTableName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型。|是|固定值为`ots`。|
|instanceName|实例名称。|是|无。|
|tableName|表名称。|是|无。|
|endPoint|实例访问地址。|是|无。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|retryIntervalMs|重试间隔时间。|否|单位毫秒，默认值为1000（1秒）。|
|maxRetryTimes|最大重试次数。|否|默认值为100。|
|connectTimeout|Connector连接Tablestore的超时时间。|否|单位毫秒，默认值为30000（30秒）。|
|socketTimeout|Connector连接Tablestore的Socket超时时间。|否|单位毫秒，默认值为30000（30秒）。|

## CACHE参数

|参数|说明|备注|
|--|--|--|
|cache|缓存策略|表格存储维表支持以下两种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小|当选择LRU缓存策略后，可以设置缓存大小，默认值为10000行。|
|cacheTTLMs|缓存超时时间|单位为毫秒，当选择LRU缓存策略后，可以设置缓存失效的超时时间。|

## 常见问题

Q：维表进行JOIN时，查询不到数据，应该如何处理？

A：检查DDL语句和物理表中的Schema类型和名称是否一致。

