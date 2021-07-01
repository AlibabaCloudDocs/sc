---
keyword: [Message Queue, result table, MQ]
---

# Create a Message Queue for Apache RocketMQ result table

This topic describes how to create a Message Queue for Apache RocketMQ result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and sample code used when you create a Message Queue for Apache RocketMQ result table.

## Introduction to Message Queue for Apache RocketMQ

Message Queue for Apache RocketMQ is a distributed messaging middleware that is developed by Alibaba Cloud based on Apache RocketMQ. It features low latency, high concurrency, high availability, and high reliability. Message Queue for Apache RocketMQ provides asynchronous decoupling and load shifting for distributed application systems. It also supports features for Internet applications, including massive message accumulation, high throughput, and reliable retry. Flink can use the streaming data that is stored in Message Queue for Apache RocketMQ as output data.

## DDL syntax

```
create table mq_sink(
   x varchar,
   y varchar,
   z varchar
) with (
   'connector'='mq',
   'topic'='<yourTopicName>',
   'endpoint'='<yourEndpoint>',
   'accessId'='<yourAccessId>',
   'accessKey'='<yourAccessSecret>'
);
```

**Note:** Message Queue for Apache RocketMQ is a messaging middleware that stores unstructured data. You do not need to define schemas for Message Queue for Apache RocketMQ tables. The schemas are determined by the service layer. Flink supports messages of Message Queue for Apache RocketMQ only in the CSV and binary formats.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `mq`.|
|topic|The name of a topic.|Yes|None.|
|endpoint|The endpoint of Message Queue for Apache RocketMQ.|Yes|Message Queue for Apache RocketMQ supports the following types of endpoints:-   Internal endpoint \(Alibaba Cloud classic network or VPC\): `onsaddr-internal.aliyun.com:8080` in the China \(Beijing\), China \(Shanghai\), China \(Hangzhou\), and China \(Shenzhen\) regions.

**Note:** Only VVR 2.1.1 and later versions can be deployed in these regions.

-   Public endpoint: `onsaddr-internet.aliyun.com:80`. |
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|producerGroup|The name of the producer group.|Yes|None.|
|tag|The message tag.|No|This parameter is empty by default.|
|nameServerSubgroup|The name server group.|No|-   Group name for internal services \(Alibaba Cloud classic network or VPC\): nsaddr4client-internal.
-   Group name for Internet services: nsaddr4client-internet

**Note:** Only VVR 2.1.1 and later versions support this parameter. |
|encoding|The encoding format.|No|Default value: `utf-8`.|
|retryTimes|The maximum number of retries for writing data to the table.|No|Default value: 10.|
|sleepTimeMs|The retry interval.|No|Default value: 1000. Unit: milliseconds.|
|instanceID|The ID of an Message Queue for Apache RocketMQ instance.|No|-   If the Message Queue for Apache RocketMQ instance does not have a separate namespace, the instanceID parameter cannot be used.
-   If the Message Queue for Apache RocketMQ instance has a separate namespace, the instanceID parameter is required. |

## Sample code

-   CSV format

    ```
    create table mq_sink (
        id INTEGER,
        len BIGINT,
        content VARCHAR
    ) with (
        'connector'='mq',
        'endpoint'='<yourEndpoint>',
        'accessId'='<yourAccessId>',
        'accessKey'='<yourAccessSecret>',
        'topic'='<yourTopicName>',
        'producerGroup'='<yourGroupName>',
        'tag'='<yourTagName>',
        'encoding'='utf-8',
        'retryTimes'='5',
        'sleepTimeMs'='500'
    );
    ```

-   Binary format

    ```
    CREATE TEMPORARY TABLE datagen_source (
        commodity VARCHAR
    ) WITH ( 
        'connector' = 'datagen' 
    );
    
    CREATE TEMPORARY TABLE mq_sink (
        mess VARBINARY
    ) WITH (
        'connector'='mq',
        'endpoint'='<yourEndpoint>',
        'accessId'='<yourAccessId>',
        'accessKey'='<yourAccessSecret>',
        'topic'='<yourTopicName>',
        'producerGroup'='<yourGroupName>'
    );
    
    INSERT INTO mq_sink
    SELECT 
         CAST(SUBSTRING(commodity,0,5) AS VARBINARY) AS mess   
    FROM datagen_source;
    ```


