---
keyword: [日志服务, 结果表]
---

# 日志服务SLS结果表

本文为您介绍日志服务SLS结果表的DDL定义、WITH参数、类型映射和相关文档。

## 什么是日志服务

[日志服务SLS](/cn.zh-CN/产品简介/什么是日志服务.md)是针对日志类数据的一站式服务。日志服务可以帮助您快捷地完成数据采集、消费、投递以及查询分析，提升运维和运营效率，建立海量日志处理能力。日志服务本身是流数据存储，Flink支持将其作为流式数据的输出。

## DDL定义

```
create table sls_sink(
 name varchar,
 age int,
 birthday int
)with(
 'connector'='sls',
 'endPoint'='<endPoint>',
 'accessId'='<yourAccessId>',
 'accessKey'='<yourAccessKey>',
 'project'='<yourProjectName>',
 'logstore'='<yourLogstoreName>'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为`sls`。|
|endPoint|EndPoint地址|是|[服务入口](/cn.zh-CN/开发指南/API 参考/服务入口.md)。|
|project|项目名|是|无|
|logstore|表名|是|无|
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|

## 类型映射

日志服务和Flink字段类型对应关系如下，建议您使用该对应关系进行DDL声明。

|日志服务字段类型|Flink字段类型|
|--------|---------|
|STRING|VARCHAR|

