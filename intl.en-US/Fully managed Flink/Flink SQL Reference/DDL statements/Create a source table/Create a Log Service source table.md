---
keyword: [Log Service, source table]
---

# Create a Log Service source table

This topic describes how to create a Log Service source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and attribute fields used when you create a Log Service source table.

## Introduction to Log Service

Log Service is an end-to-end data logging service. Log Service helps you collect, consume, ship, query, and analyze log data in a quick manner. It improves the operations and maintenance \(O&M\) efficiency, and allows you to process large amounts of data.

## DDL syntax

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

**Note:**

-   Log Service does not support the MAP data type.
-   Log Service sets the fields that do not exist to null.
-   We recommend that you define the fields in the source table in the same order as the fields in the table of the upstream storage system. Unordered fields are also supported.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `sls`.|
|endPoint|The endpoint of Log Service.|Yes|[Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md)|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|project|The name of the Log Service project that you want to access.|Yes|None.|
|logStore|The name of the Logstore in the Log Service project.|Yes|None.|
|startTime|The time at which logs start to be consumed.|Yes|None.|
|consumerGroup|The name of a consumer group.|No|You can specify this parameter as required. The format of the name is not fixed.|
|batchGetSize|The number of log items that are read from a log group at a time.|No|Default value: 10.**Note:**

-   The value of the batchGetSize parameter cannot exceed 1000. Otherwise, an error is returned.
-   This parameter specifies the number of log items that are read from a log group at a time. If both the size of a log item and the value of the batchGetSize parameter are large, frequent garbage collections may occur. To avoid this issue, you can set the batchGetSize parameter to a small value. |

## Field type mapping

The following table describes the data type mappings between Log Service and Flink fields. We recommend that you declare the mappings in a DDL statement.

|Data type of Log Service|Data type of Flink|
|------------------------|------------------|
|STRING|VARCHAR|

## Attribute fields

Flink cannot obtain attribute fields of Log Service. The following table lists these attribute fields.

|Field|Description|
|-----|-----------|
|`__source__`|The message source.|
|`__topic__`|The message topic.|
|`__timestamp__`|The time at which a log was generated.|

