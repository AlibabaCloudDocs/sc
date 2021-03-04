---
keyword: [Log Service, result table]
---

# Create a Log Service result table

This topic describes how to create a Log Service result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and references used when you create a Log Service result table.

## Introduction to Log Service

Log Service is an end-to-end data logging service that is developed by Alibaba Cloud. Log Service allows you to collect, consume, ship, query, and analyze log data in a quick manner. It improves the operations and maintenance \(O&M\) efficiency, and provides the capability to process large amounts of data. Log Service is used to store streaming data. This way, Flink can use the streaming data that is stored in Log Service as output data.

## DDL syntax

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

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `sls`.|
|endPoint|The endpoint of Log Service.|Yes|[Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md).|
|project|The name of a project.|Yes|None.|
|logstore|The name of the table.|Yes|None.|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|flushIntervalMs|The interval at which data writing is triggered.|No|Default value: 2000. Unit: milliseconds.|

## Field type mapping

The following table lists the data type mappings between Log Service and Flink fields. We recommend that you declare the mappings in the DDL statement.

|Data type of Log Service|Data type of Flink|
|------------------------|------------------|
|STRING|VARCHAR|

## References

For more information about Log Service, see [What is Log Service?](/intl.en-US/Product Introduction/What is Log Service?.md).

