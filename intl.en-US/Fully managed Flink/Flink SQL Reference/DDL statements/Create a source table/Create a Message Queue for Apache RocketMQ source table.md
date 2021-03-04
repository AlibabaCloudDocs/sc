---
keyword: [Message Queue, source table, MQ]
---

# Create a Message Queue for Apache RocketMQ source table

This topic describes how to create a Message Queue for Apache RocketMQ source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a Message Queue for Apache RocketMQ source table.

## Introduction to Message Queue for Apache RocketMQ

Message Queue for Apache RocketMQ is a distributed messaging middleware that is developed by Alibaba Cloud based on Apache RocketMQ. It features low latency, high concurrency, high availability, and high reliability. Message Queue for Apache RocketMQ provides asynchronous decoupling and load shifting for distributed application systems. It also supports features for Internet applications, including massive message accumulation, high throughput, and reliable retry.

## DDL syntax

```
create table mq_source(
   x varchar,
   y varchar,
   z varchar
) with (
   'connector'='mq',
   'topic'='<yourTopicName>',
   'endpoint'='<yourEndpoint>',
   'pullIntervalMs'='1000',
   'accessId'='<yourAccessId>',
   'accessKey'='<yourAccessSecret>',
   'startMessageOffset'='1000',
   'consumerGroup'='<yourConsumerGroup>',
   'fieldDelimiter'='|'
);
```

**Note:** Message Queue for Apache RocketMQ is a messaging middleware that stores unstructured data. You do not need to define schemas for Message Queue for Apache RocketMQ tables. The schemas are determined by the service layer. Flink supports messages of Message Queue for Apache RocketMQ only in the CSV and binary formats.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `mq`.|
|topic|The name of a topic.|Yes|None.|
|endPoint|The endpoint of Message Queue for Apache RocketMQ.|Yes|Message Queue for Apache RocketMQ supports the following types of endpoints: -   Internal endpoint \(Alibaba Cloud classic network or VPC\): `onsaddr-internal.aliyun.com:8080` in the China \(Beijing\), China \(Shanghai\), China \(Hangzhou\), and China \(Shenzhen\) regions.

**Note:** Only VVR 2.1.1 and later versions can be deployed in these regions.

-   Public endpoint: `onsaddr-internet.aliyun.com:80`. |
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|consumerGroup|The name of a consumer group.|Yes|None.|
|pullIntervalMs|The interval at which messages are pulled.|Yes|Unit: milliseconds.|
|nameServerSubgroup|The name server group.|No|-   Group name for internal services \(Alibaba Cloud classic network or VPC\): nsaddr4client-internal.
-   Group name for Internet services: nsaddr4client-internet

**Note:** Only VVR 2.1.1 and later versions support this parameter. |
|startTime|The start time of message consumption.|No|None.|
|startMessageOffset|The offset from the start time.|No|This parameter is optional. If you specify this parameter, messages are consumed from the time point specified by this parameter.|
|tag|The subscription tag.|No|None.|
|lineDelimiter|The row delimiter used when a message block is parsed.|No|Default value: `\n`.|
|fieldDelimiter|A field delimiter.|No|Default value: `\u0001`. `\u0001` is the field delimiter in read-only mode, and `^A` is the field delimiter in edit mode. `\u0001` is invisible in read-only mode.|
|encoding|The encoding format.|No|Default value: `utf-8`.|
|lengthCheck|The policy for checking the number of fields parsed from a row of data.|No|Default value: NONE. -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is extracted from left to right based on the order of defined fields.
-   If the number of fields parsed from a row of data is less than the number of defined fields, this row of data is skipped.

Other valid values are SKIP, EXCEPTION, and PAD. -   SKIP: If the number of fields parsed from a row of data does not match the number of defined fields, this row of data is skipped.
-   EXCEPTION: If the number of fields parsed from a row of data does not match the number of defined fields, an exception is reported.
-   PAD: Data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the number of defined fields, the values of the missing fields are padded with null. |
|columnErrorDebug|Specifies whether debugging is enabled.|No|Default value: FALSE. If you set this parameter to TRUE, a log entry is printed when a parsing exception occurs.|
|instanceID|The ID of the instance.|No|Perform the following operations based on whether the Message Queue for Apache RocketMQ instance has a separate namespace:-   If the instance has a separate namespace, you must specify the instanceID parameter.
-   If the instance does not have a separate namespace, you do not need to specify the instanceID parameter. |

## Field data mapping

|Data type of Message Queue for Apache RocketMQ|Data type of Flink|
|----------------------------------------------|------------------|
|STRING|VARCHAR|

## Sample code

-   CSV format

    Assume that you have an Message Queue for Apache RocketMQ message in the following CSV format:

    ```
    1,name,male 
    2,name,female
    ```

    **Note:** A Message Queue for Apache RocketMQ message can contain any number of data records. Multiple data records are separated by `\n`.

    You can use the following DDL statement to declare a Message Queue for Apache RocketMQ source table in a Flink job:

    ```
    create table mq_source(
       id varchar,
       name varchar,
       gender varchar
    ) with (
       'connector'='mq',
       'topic'='<yourTopicName>',
       'endpoint'='<yourEndpoint>',
       'pullIntervalMs'='1000',
       'accessId'='<yourAccessId>',
       'accessKey'='<yourAccessSecret>',
       'startMessageOffset'='1000',
       'consumerGroup'='<yourConsumerGroup>',
       'fieldDelimiter'='|'
    );
    ```

-   Binary format

    ```
    create table mq_source (
       mess varbinary
    ) with (
       'connector'='mq',
       'endpoint'='<yourEndpoint>',
       'pullIntervalMs'='500',
       'accessId'='<yourAccessId>',
       'accessKey'='<yourAccessSecret>',
       'topic'='<yourTopicName>',
       'consumerGroup'='<yourConsumerGroup>'
    );
    ```


