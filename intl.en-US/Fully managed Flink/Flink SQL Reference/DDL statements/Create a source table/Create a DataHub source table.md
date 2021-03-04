---
keyword: [DataHub, source table]
---

# Create a DataHub source table

This topic describes how to create a DataHub source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, attribute fields, and FAQ used when you create a DataHub source table.

## Introduction to DataHub

DataHub is a real-time data distribution platform designed to process streaming data. You can publish and subscribe to applications for streaming data in DataHub and distribute the data to other platforms. DataHub allows you to analyze streaming data and build applications based on the streaming data.

## DDL syntax

```
create table datahub_source(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='datahub',
  'endPoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'subId'='<yourSubId>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `datahub`.|
|endPoint|The endpoint of DataHub.|Yes|For more information, see [List of DataHub endpoints](https://help.aliyun.com/document_detail/158778.html?spm=a2c4g.11186623.6.547.77a91fd1eveQrC).|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|project|A project in DataHub.|Yes|None.|
|topic|A topic of the project.|Yes|None.|
|subId|The subscription ID of a topic.|Yes|Multiple tasks cannot use the same subscription ID at the same time.|
|startTime|The start time of DataHub logs.|No|The format is `yyyy-MM-dd hh:mm:ss`.|
|retryTimeout|The maximum timeout period fo a retry.|No|Default value: 180000. Unit: milliseconds.|
|retryInterval|The interval of retries.|No|Default value: 1000. Unit: milliseconds.|
|maxFetchSize|The number of data records that can be read at a time.|No|Default value: 50.|
|maxBufferSize|The maximum number of cached data records that are read asynchronously.|No|Default value: 50.|
|lengthCheck|The policy for checking the number of fields parsed from a row of data.|No|-   NONE: the default value.
    -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the number of defined fields, this row of data is skipped.
-   SKIP: If the number of fields parsed from a row of data does not match the number of defined fields, this row of data is skipped.
-   EXCEPTION: If the number of fields parsed from a row of data does not match the number of defined fields, an exception is reported.
-   PAD: Data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the number of defined fields, the values of the missing fields are padded with null from left to right. |
|columnErrorDebug|Specifies whether debugging is enabled.|No|-   false: the default value. Debugging is disabled.
-   true: Debugging is enabled, and logs that contain parsing exceptions are printed. |

**Note:** If you use the BLOB type, you must declare fields as VARBINARY, which is similar to METAQ.

## Field type mapping

The following table lists the data type mappings between DataHub and Flink fields. We recommend that you declare the mappings in a DDL statement.

|Data type of DataHub|Data type of Flink|
|--------------------|------------------|
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

## Attribute field

You can use Flink SQL to retrieve the attribute field TIMESTAMP from a DataHub source table. You can obtain the system time at which each data record is written to DataHub after the attribute field is read.

|Field|Description|
|-----|-----------|
|TIMESTAMP|The system time at which each data record is written to DataHub.|

![Shard data sampling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8890256951/p64847.png)

**Note:** For more information about how to obtain attribute fields, see [Obtain attribute fields of a source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md).

## Sample code

```
CREATE TEMPORARY table datahub_source(
  name VARCHAR
) with (
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'subId'='<yourSubId>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>',
  'startTime'='2018-06-01 00:00:00'
);

CREATE TEMPORARY table datahub_sink(
  name  VARCHAR  
) with (
  'connector'='datahub',
  'endpoint'='<endPoint>',
  'project'='<yourProjectName>',
  'topic'='<yourTopicName>',
  'accessId'='<yourAccessId>',
  'accessKey'='<yourAccessKey>'
);

INSERT INTO datahub_sink
SELECT 
  LOWER(name)
from datahub_source;
```

## FAQ

-   Q: After a DataHub topic is split or scaled in, a Flink job fails to run. How do I resume the job?

    A: If a topic that is being read by Flink is split or scaled in, the Flink job fails and cannot resume. To resume the job, you must stop and restart it.

-   Q: Can I delete a DataHub topic that is being consumed?

    A: No. You cannot delete or recreate DataHub topics that are being consumed.


