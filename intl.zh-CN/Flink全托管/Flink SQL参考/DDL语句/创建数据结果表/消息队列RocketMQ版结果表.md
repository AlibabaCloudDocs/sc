---
keyword: [消息队列, 结果表, MQ]
---

# 消息队列RocketMQ版结果表

本文为您介绍消息队列RocketMQ版结果表DDL定义、WITH参数和示例代码等。

## 什么是消息队列RocketMQ版

消息队列 RocketMQ版是阿里云基于Apache RocketMQ构建的低延迟、高并发、高可用和高可靠的分布式消息中间件。消息队列RocketMQ版既可为分布式应用系统提供异步解耦和削峰填谷的能力，同时也具备互联网应用所需的海量消息堆积、高吞吐和可靠重试等特性。Flink支持将消息队列MQ作为流式数据的输出。

## DDL定义

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

**说明：** MQ是非结构化存储格式的消息中间件，对于数据的Schema不提供强制定义，完全由业务层指定。Flink仅支持CSV和二进制格式的MQ消息。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为`mq`。|
|topic|topic名称|是|无|
|endpoint|地址|是|阿里云消息队列RocketMQ版接入地址支持以下两种类型： -   内网服务（阿里云经典网络/VPC）： 华北2（北京）、华东2（上海）、华东1（杭州）、华南1（深圳）：`onsaddr-internal.aliyun.com:8080`。

**说明：** 仅VVR 2.1.1及以上版本支持以上地域。

-   公网服务：`onsaddr-internet.aliyun.com:80`。 |
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|producerGroup|写入的群组|是|无|
|tag|写入的标签|否|默认值为空。|
|nameServerSubgroup|NameServer组|否|-   内网服务（阿里云经典网络/VPC）：nsaddr4client-internal
-   公网服务：nsaddr4client-internet

**说明：** 仅VVR 2.1.1及以上版本支持该参数。 |
|encoding|编码类型|否|默认值为`utf-8`。|
|retryTimes|写入的重试次数|否|默认值为10。|
|sleepTimeMs|重试间隔时间|否|默认值为1000（毫秒）。|
|instanceID|Topic所属的分组|否|-   如果MQ实例无独立命名空间，则不可以使用instanceID参数。
-   如果MQ实例有独立命名空间，则instanceID参数必选。 |

## 代码示例

-   CSV格式

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

-   二进制格式

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


