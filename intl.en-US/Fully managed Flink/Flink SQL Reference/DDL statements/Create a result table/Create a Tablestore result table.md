---
keyword: [Tablestore, result table]
---

# Create a Tablestore result table

This topic describes how to create a Tablestore result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and field type mappings used when you create a Tablestore result table.

## Introduction to Tablestore

Tablestore is a distributed NoSQL database service that is built on the Apsara distributed operating system of Alibaba Cloud. Tablestore adopts sharding and load balancing technologies to seamlessly scale out services and handle concurrent transactions. You can use Tablestore to store and query large amounts of structured data in real time. Flink can use the streaming data that is stored in Tablestore as output data.

## DDL syntax

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

**Note:** You must define a `primary key` for a Tablestore result table. Latest output data is appended to the Tablestore result table to update the result. For more information about update methods, see [Update type](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Overview of result tables.md).

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `ots`.|
|instanceName|The name of the instance.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|endPoint|The endpoint of the instance.|Yes|For more information, see [Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|valueColumns|The columns that you want to insert.|Yes|Separate multiple columns with commas \(,\), for example, `ID,NAME`.|
|bufferSize|The maximum number of data records that can be stored in the buffer before data deduplication is triggered.|No|Default value: 5000. This value indicates that data deduplication is triggered if the number of input data records in the buffer reaches 5,000.|
|batchWriteTimeoutMs|The write timeout period.|No|Default value: 5000. Unit: milliseconds. This value indicates that if the number of input data records does not reach the value specified by the batchSize parameter within 5,000 milliseconds, all cached data is written to the result table.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 100.|
|retryIntervalMs|The retry interval.|No|Default value: 1000. Unit: milliseconds.|
|maxRetryTimes|The maximum number of retries that are allowed to write data to the table.|No|Default value: 10.|
|ignoreDelete|Specifies whether to ignore DELETE operations.|No|Default value: False.|
|connectTimeout|The timeout period for a connector to connect to Tablestore.|No|Default value: 30000. Unit: milliseconds.|
|socketTimeout|The socket timeout period for a connector to connect to Tablestore.|No|Default value: 30000. Unit: milliseconds.|

## Field type mapping

|Data type of Tablestore|Data type of Flink|
|-----------------------|------------------|
|INTEGER|BIGINT|
|STRING|STRING|
|BOOLEAN|BOOLEAN|
|DOUBLE|DOUBLE|

