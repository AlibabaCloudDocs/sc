---
keyword: [Message Queue, source table, MQ]
---

# Create a Message Queue for Apache RocketMQ source table

This topic describes how to create a Message Queue for Apache RocketMQ source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a Message Queue for Apache RocketMQ source table.

**Note:** The Message Queue for Apache RocketMQ connector can be used to store data of a source table for streaming jobs and batch jobs.

## Introduction to Message Queue for Apache RocketMQ

[Message Queue for Apache RocketMQ]() is a distributed messaging middleware that is developed by Alibaba Cloud based on Apache RocketMQ. Message Queue for Apache RocketMQ features low latency, high concurrency, high availability, and high reliability. Message Queue for Apache RocketMQ provides asynchronous decoupling and load shifting for distributed application systems. It also supports features for Internet applications, including massive message accumulation, high throughput, and reliable retry.

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

**Note:** Message Queue for Apache RocketMQ is a messaging middleware that stores unstructured data. You do not need to define schemas for Message Queue for Apache RocketMQ tables. The schemas are specified by the service layer. Flink supports messages of Message Queue for Apache RocketMQ only in the CSV and binary formats.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `mq`.|
|topic|The name of a topic.|Yes|None.|
|endPoint|The endpoint of Message Queue for Apache RocketMQ.|Yes|Message Queue for Apache RocketMQ supports the following types of endpoints:-   For jobs that run on Ververica Runtime \(VVR\) 3.0.1 or later, use Transmission Control Protocol \(TCP\) endpoints. For more information, see [Announcement on internal TCP endpoint settings](). To obtain endpoints, perform the following operations:
    -   Internal endpoints for Message Queue for Apache RocketMQ \(Alibaba Cloud classic network or VPC\): Log on to the Message Queue for Apache RocketMQ console. In the left-side navigation pane, click Instances. On the page that appears, find the required instance, and click Details in the Actions column. On the Instance Details page, click the **Endpoints** tab. You can view the endpoint that corresponds to **Internal Access** in the **TCP Endpoint** section.
    -   Public endpoint for Message Queue for Apache RocketMQ: Log on to the Message Queue for Apache RocketMQ console. In the left-side navigation pane, click Instances. On the page that appears, find the required instance, and click Details in the Actions column. On the Instance Details page, click the **Endpoints** tab. You can view the endpoint that corresponds to **Internet Access** in the **TCP Endpoint** section.
-   For jobs that run on VVR of a version earlier than 3.0.1, use the following endpoints:
    -   Internal endpoints for Message Queue for Apache RocketMQ \(Alibaba Cloud classic network or VPC\):
        -   China \(Hangzhou\), China \(Shanghai\), China \(Qingdao\), China \(Beijing\), China \(Shenzhen\), and China \(Hong Kong\): `onsaddr-internal.aliyun.com:8080`.
        -   Singapore \(Singapore\): `ap-southeastaddr-internal.aliyun.com:8080`.
        -   UAE \(Dubai\): `ons-me-east-1-internal.aliyuncs.com:8080`.
        -   India \(Mumbai\): `ons-ap-south-1-internal.aliyuncs.com:8080`.
        -   Malaysia \(Kuala Lumpur\): `ons-ap-southeast-3-internal.aliyun.com:8080`.
    -   Public endpoint for Message Queue for Apache RocketMQ: `onsaddr-internet.aliyun.com:80`. |
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|consumerGroup|The name of a consumer group.|Yes|None.|
|pullIntervalMs|The interval at which messages are pulled.|Yes|Unit: milliseconds.|
|nameServerSubgroup|The name server group.|No|-   For the internal Message Queue for Apache RocketMQ service \(Alibaba Cloud classic network or VPC\), you must set this parameter to nsaddr4client-internal.
-   For the Internet Message Queue for Apache RocketMQ service, you do not need to configure this parameter.

**Note:** Only VVR versions from 2.1.1 to 3.0.0 support this parameter. VVR 3.0.1 and later versions do not support this parameter. |
|timeZone|The time zone.|No|Example: Asia/Shanghai.|
|startTime|The start time to consume messages.|No|startTime must be used with timeZone. When you configure startTime, you must also configure timeZone, such as 'timeZone' = 'Asia/Shanghai'.|
|startMessageOffset|The start offset to consume messages.|No|This parameter is optional. If you specify this parameter, messages are consumed from the time specified by this parameter.|
|tag|The subscription tag.|No|None.|
|lineDelimiter|The row delimiter used when a message block is parsed.|No|Default value: `\n`.|
|fieldDelimiter|A field delimiter.|No|Default value: `\u0001`. `\u0001` is the field delimiter in read-only mode, and `^A` is the field delimiter in edit mode. `\u0001` is invisible in read-only mode.|
|encoding|The encoding format.|No|Default value: `utf-8`.|
|lengthCheck|The policy for checking the number of fields parsed from a row of data.|No|Default value: NONE. -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is extracted from left to right based on the order of defined fields.
-   If the number of fields parsed from a row of data is less than the number of defined fields, this row of data is skipped.

Other valid values are SKIP, EXCEPTION, and PAD. -   SKIP: If the number of fields parsed from a row of data does not match the number of defined fields, this row of data is skipped.
-   EXCEPTION: If the number of fields parsed from a row of data is different from the number of defined fields, an exception is reported.
-   PAD: Data is padded from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is greater than the number of defined fields, data is extracted from left to right based on the order of defined fields.
    -   If the number of fields parsed from a row of data is less than the number of defined fields, the values of the missing fields are padded with null. |
|columnErrorDebug|Specifies whether debugging is enabled.|No|Default value: FALSE. If you set this parameter to TRUE, a log entry is displayed when a parsing exception occurs.|
|instanceID|The ID of the instance.|No|Perform the following operations based on whether the Message Queue for Apache RocketMQ instance has a separate namespace:-   If the instance has a separate namespace, you must specify the instanceID parameter.
-   If the instance does not have a separate namespace, you do not need to specify the instanceID parameter. |

## Field type mapping

|Data type of Message Queue for Apache RocketMQ|Data type of Flink|
|----------------------------------------------|------------------|
|STRING|VARCHAR|

## Sample code

-   CSV format

    The following example shows a Message Queue for Apache RocketMQ message in the CSV format.

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


