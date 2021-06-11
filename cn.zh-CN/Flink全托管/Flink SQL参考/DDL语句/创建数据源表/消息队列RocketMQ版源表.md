---
keyword: [消息队列, 源表, RocketMQ]
---

# 消息队列RocketMQ版源表

本文为您介绍消息队列RocketMQ版源表DDL定义、WITH参数、类型映射、属性字段和代码示例。

**说明：** RocketMQ Connector可以作为Stream作业和Batch作业的源表使用。

## 什么是消息队列RocketMQ版

[消息队列RocketMQ版]()是阿里云基于Apache RocketMQ构建的低延迟、高并发、高可用和高可靠的分布式消息中间件。消息队列RocketMQ版既可为分布式应用系统提供异步解耦和削峰填谷的能力，同时也具备互联网应用所需的海量消息堆积、高吞吐和可靠重试等特性。

## DDL定义

```
create table mq_source(
  x varchar,
  y varchar,
  z varchar
) with (
  'connector' = 'mq',
  'topic' = '<yourTopicName>',
  'endpoint' = '<yourEndpoint>',
  'pullIntervalMs' = '1000',
  'accessId' = '<yourAccessId>',
  'accessKey' = '<yourAccessSecret>',
  'startMessageOffset' = '1000',
  'consumerGroup' = '<yourConsumerGroup>',
  'fieldDelimiter' = '|'
);
```

**说明：** RocketMQ是非结构化存储格式的消息中间件，对于数据的Schema不提供强制定义，完全由业务层指定。Flink仅支持CSV和二进制格式的RocketMQ消息。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|源表类型。|是|固定值为`mq`。|
|topic|topic名称。|是|无。|
|endPoint|EndPoint地址。|是|阿里云消息队列RocketMQ版接入地址支持以下两种类型：-   VVR 3.0.1及以上版本的作业，需要使用TCP协议客户端接入点，详情请参见 [关于TCP内网接入点设置的公告]()。接入点获取方式如下：
    -   内网服务MQ（阿里云经典网络/VPC）接入地址：在MQ控制台目标实例详情中，选择**接入点** \> **TCP协议客户端接入点** \> **内网访问**，获取对应的EndPoint。
    -   公网服务MQ接入地址：在MQ控制台目标实例详情中，选择**接入点** \> **TCP协议客户端接入点** \> **公网访问**，获取对应的EndPoint。
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
-   内网服务无法跨域访问。例如，您所购买的实时计算服务的地域为华东1，但是购买的消息队列MQ服务的地域为华东2（上海），则无法访问。
-   由于阿里云网络安全策略动态变化，实时计算连接公网服务MQ时可能会出现网络连接问题，推荐您使用内网服务MQ，如果在使用公网服务MQ时出现异常，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?accounttraceid=f7b76db740fa486baa4b63bd5848fbc1idrb)进行咨询。 |
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|consumerGroup|Consumer组名。|是|无。|
|pullIntervalMs|拉取数据的时间间隔。|是|单位为毫秒。|
|nameServerSubgroup|NameServer组。|否|-   内网服务（阿里云经典网络或VPC）：必须配置'nameServerSubgroup' = 'nsaddr4client-internal'。
-   公网服务：无需配置nameServerSubgroup。

**说明：** 仅VVR 2.1.1 ~ VVR 3.0.0版本支持该参数，VVR 3.0.1及以后版本不支持该参数。 |
|timeZone|时区。|否|例如，Asia/Shanghai。|
|startTimeMs|启动时间点。|否|无。|
|startMessageOffset|消息开始的偏移量。|否|如果填写该参数，则优先以startMessageoffset的位点开始加载数据。|
|tag|订阅的标签。|否|无。|
|lineDelimiter|解析Block时，行分隔符。|否|默认值为 `\n`。|
|fieldDelimiter|字段分隔符。|否|根据MQ终端的模式，分隔符分别为：-   在只读模式下（默认模式），分隔符为`u&'\0001`。该模式下，分隔符不可见。
-   在编辑模式下，分隔符为`^A`。 |
|encoding|编码格式。|否|默认值为`utf-8`。|
|lengthCheck|单行字段条数检查策略。|否|默认值为NONE，表示： -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
-   解析出的字段数小于定义字段数时，跳过这行数据。

其它可选值为SKIP、EXCEPTION和PAD。 -   SKIP：解析出的字段数和定义字段数不同时跳过这行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，在行尾用null填充缺少的字段。 |
|columnErrorDebug|是否打开调试开关。|否|默认值为FALSE。如果设置为TRUE，则打印解析异常的Log。|
|instanceID|实例ID。|否|根据RocketMQ实例是否有独立命名空间，执行如下操作：-   是，必须配置instanceID参数。
-   否，不能配置instanceID参数。 |

## 类型映射

|RocketMQ字段类型|Flink字段类型|
|------------|---------|
|STRING|VARCHAR|

## 属性字段

|字段名|字段类型|说明|
|---|----|--|
|topic|VARCHAR METADATA VIRTUAL|RocketMQ消息Topic。|
|queue-id|INT METADATA VIRTUAL|RocketMQ消息队列ID。|
|queue-offset|BIGINT METADATA VIRTUAL|RocketMQ消息队列的消费位点。|
|msg-id|VARCHAR METADATA VIRTUAL|RocketMQ消息ID。|
|store-timestamp|TIMESTAMP\(3\) METADATA VIRTUAL|RocketMQ消息存储时间。|
|born-timestamp|TIMESTAMP\(3\) METADATA VIRTUAL|RocketMQ消息生成时间。|
|keys|VARCHAR METADATA VIRTUAL|RocketMQ消息Keys。|
|tags|VARCHAR METADATA VIRTUAL|RocketMQ消息Tags。|

**说明：** 仅在VVR 3.0.1及以上版本支持获取以上RocketMQ属性字段。

## 代码示例

-   CSV格式

    假设您的1条CSV格式消息记录如下。

    ```
    1,name,male 
    2,name,female
    ```

    **说明：** 1条RocketMQ消息可以包括0条到多条数据记录，记录之间使用`\n`分隔。

    Flink作业中，声明RocketMQ数据源表的DDL如下。

    ```
    create table mq_source(
       id varchar,
       name varchar,
       gender varchar,
       topic varchar metadata virtual
    ) with (
       'connector' = 'mq',
       'topic' = 'mq-test',
       'endpoint' = '<yourEndpoint>',
       'pullIntervalMs' = '1000',
       'accessId' = '<yourAccessId>',
       'accessKey' = '<yourAccessSecret>',
       'startMessageOffset' = '1000',
       'consumerGroup' = 'mq-group',
       'fieldDelimiter' = '|'
    );
    ```

-   二进制格式

    ```
    create temporary table source_table (
      mess varbinary
    ) with (
      'connector' = 'mq',
      'endpoint' = '<yourEndpoint>',
      'pullIntervalMs' = '500',
      'accessId' = '<yourAccessId>',
      'accessKey' = '<yourAccessSecret>',
      'topic' = 'mq-test',
      'consumerGroup' = 'mq-group'
    );
    
    create temporary table out_table (
      commodity varchar
    ) with (
      'connector' = 'print'
    );
    
    insert into out_table
    select 
      cast(mess as varchar)
    from source_table;
    ```


