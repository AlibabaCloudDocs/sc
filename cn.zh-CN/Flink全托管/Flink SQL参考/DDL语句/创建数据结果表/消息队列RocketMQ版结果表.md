---
keyword: [消息队列, 结果表, MQ]
---

# 消息队列RocketMQ版结果表

本文为您介绍消息队列RocketMQ版结果表DDL定义、WITH参数和示例代码等。

**说明：** RocketMQ Connector可以作为Stream作业和Batch作业的结果表使用。

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
|endpoint|地址|是|阿里云消息队列RocketMQ版接入地址支持以下两种类型：-   VVR 3.0.1及以上版本的作业，需要使用TCP协议客户端接入点，详情请参见 [关于TCP内网接入点设置的公告]()。接入点获取方式如下：
    -   内网服务MQ（阿里云经典网络/VPC）接入地址：在MQ控制台目标实例详情中，选择**接入点** \> **TCP协议客户端接入点** \> **内网访问**，获取对应的endPoint。
    -   公网服务MQ接入地址：在MQ控制台目标实例详情中，选择**接入点** \> **TCP协议客户端接入点** \> **公网访问**，获取对应的endPoint。
-   VVR 3.0.1（不含）以下版本的作业，使用如下接入点：
    -   内网服务MQ（阿里云经典网络/VPC）接入地址：
        -   华东1（杭州）、华东2（上海）、华北1（青岛）、华北2（北京）、华南1（深圳）、中国（香港）：`onsaddr-internal.aliyun.com:8080`
        -   亚太东南1（新加坡）：`ap-southeastaddr-internal.aliyun.com:8080`。
        -   中东东部1（迪拜）：`ons-me-east-1-internal.aliyuncs.com:8080`。
        -   亚太南部1（孟买）：`ons-ap-south-1-internal.aliyuncs.com:8080`。
        -   亚太东南3（吉隆坡）：`ons-ap-southeast-3-internal.aliyun.com:8080`。
    -   公网服务MQ接入地址：`onsaddr-internet.aliyun.com:80`。

**说明：**

-   如果您已使用了VVR 3.0.1（不含）以下版本的RocketMQ Connector，则您需要将您的实时计算作业升级至VVR 3.0.1及以上版本，并将作业中EndPoint参数取值更改为新的RocketMQ接入点，旧的RocketMQ接入点存在稳定性风险或不可用的问题，详情请参见[RocketMQ接入点变更导致实时计算作业适配升级公告](/cn.zh-CN/Flink全托管/产品公告.md)。
-   内网服务无法跨域访问。例如，您所购买的实时计算服务的地域为华东1，但是购买的消息队列MQ服务的地域为华东2，则无法访问。
-   由于阿里云网络安全策略动态变化，实时计算连接公网服务MQ时可能会出现网络连接问题，推荐您使用内网服务MQ，如果在使用公网服务MQ时出现异常，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?accounttraceid=f7b76db740fa486baa4b63bd5848fbc1idrb)进行咨询。 |
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|producerGroup|写入的群组|是|无|
|tag|写入的标签|否|默认值为空。|
|nameServerSubgroup|NameServer组|否|-   内网服务（阿里云经典网络/VPC）：nsaddr4client-internal
-   公网服务：nsaddr4client-internet

**说明：** 仅VVR 2.1.1 ~ VVR 3.0.0版本支持该参数，VVR 3.0.1及以后版本不支持该参数。 |
|encoding|编码类型|否|默认值为`utf-8`。|
|retryTimes|写入的重试次数|否|默认值为10。|
|sleepTimeMs|重试间隔时间|否|默认值为1000（毫秒）。|
|instanceID|MQ实例ID|否|-   如果MQ实例无独立命名空间，则不可以使用instanceID参数。
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


