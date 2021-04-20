---
keyword: [消息队列, 源表, MQ]
---

# 创建消息队列MQ源表

本文为您介绍实时计算如何创建消息队列MQ源表以及创建过程涉及到的CSV类格式、WITH参数和类型映射。

**说明：** 如果您需要使用带独立命名空间的MQ，请使用Blink 3.x作业版本。

## 什么是消息队列MQ

消息队列MQ是阿里云专业消息中间件，是企业级互联网架构的核心产品。实时计算可以将消息队列作为流式数据输入。

## 示例

```
create table mq_stream(
  x varchar,
  y varchar,
  z varchar
) with (
  type='mq',
  topic='<yourTopicName>',
  endpoint='<yourEndpoint>',
  pullIntervalMs='1000',
  accessId='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  startMessageOffset='1000',
  consumerGroup='<yourConsumerGroup>',
  fieldDelimiter='|'
);
```

**说明：** MQ实际上是一个非结构化存储格式，对于数据的Schema不提供强制定义，完全由业务层指定。目前实时计算支持类CSV格式文本和二进制格式。

## CSV格式

假设您的1条CSV格式消息记录如下。

```
1,name,male 
2,name,female
```

**说明：** 1条MQ消息可以包括0条到多条数据记录，记录之间使用`\n`分隔。

在实时计算作业中，声明MQ数据源表的DDL如下。

```
create table mq_stream(
  id varchar,
  name varchar,
  gender varchar
) with (
  type='mq',
  topic='<yourTopicName>',
  endpoint='<ourEndpoint>',
  pullIntervalMs='1000',
  accessId='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  startMessageOffset='1000',
  consumerGroup='<yourConsumerGroup>',
  fieldDelimiter='|'
);
```

## 二进制格式

二进制格式测试代码如下。

```
create table source_table (
  mess varbinary
) with (
  type = 'mq',
  endpoint = '<yourEndpoint>',
  pullIntervalMs='500',
  accessId='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  topic = '<yourTopicName>',
  consumerGroup='<yourConsumerGroup>'
);

create table out_table (
  commodity varchar
) with (
  type='print'
);

INSERT INTO out_table
SELECT 
  cast(mess as varchar)
FROM source_table;
```

**说明：**

-   `cast(mess as varchar)`需在实时计算2.0及以上版本使用，如果版本低于2.0，请先升级。
-   VARBINARY只能入参一次。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|源表类型|是|固定值为mq。|
|topic|topic名称|是|无。|
|endPoint|endPoint地址|是|阿里云消息队列提供内网服务MQ（非公网region）和公网服务MQ（公网region）两种类型，请务必根据您购买的MQ的类型选择对应正确的接入地址（endPoint）：-   Blink 3.7.10及以上版本的作业，需要使用TCP协议客户端接入点，详情请参见 [关于TCP内网接入点设置的公告]()。接入点获取方式如下：
    -   内网服务MQ（阿里云经典网络/VPC）接入地址：在MQ控制台目标实例详情中，选择**接入点** \> **TCP协议客户端接入点** \> **内网访问**，获取对应的endPoint。
    -   公网服务MQ接入地址：在MQ控制台目标实例详情中，选择**接入点** \> **TCP协议客户端接入点** \> **公网访问**，获取对应的endPoint。
-   Blink 3.7.10（不含）以下版本的作业，使用如下接入点：

    -   内网服务MQ（阿里云经典网络/VPC）接入地址：
        -   华东1（杭州）、华东2（上海）、华北1（青岛）、华北2（北京）、华南1（深圳）、中国（香港）：`onsaddr-internal.aliyun.com:8080`
        -   亚太东南1（新加坡）：`ap-southeastaddr-internal.aliyun.com:8080`。
        -   中东东部1（迪拜）：`ons-me-east-1-internal.aliyuncs.com:8080`。
        -   亚太南部1（孟买）：`ons-ap-south-1-internal.aliyuncs.com:8080`。
        -   亚太东南3（吉隆坡）：`ons-ap-southeast-3-internal.aliyun.com:8080`。
    -   公网服务MQ接入地址：`onsaddr-internet.aliyun.com:80`。
**说明：** 如果您已使用了Blink 3.7.10（不含）以下版本的RocketMQ Connector，则您需要将您的实时计算作业升级至Blink 3.7.10及以上版本，并将作业中EndPoint参数取值更改为新的RocketMQ接入点，旧的RocketMQ接入点存在稳定性风险或不可用的问题，详情请参见[RocketMQ接入点变更导致实时计算作业适配升级公告](/cn.zh-CN/Flink全托管/产品公告.md)。


**说明：**

-   内网服务无法跨域访问。例如，您所购买的实时计算服务的地域为华东1，但是购买的消息队列MQ服务的地域为华东2，则无法访问。
-   独享集群默认不能访问公网，如果需要请配置NAT网关，详情参见[独享集群如何访问公网？](/cn.zh-CN/Blink独享/共享集群（原产品线）/常见问题/如何使用.md)。
-   由于阿里云网络安全策略动态变化，实时计算连接公网服务MQ时可能会出现网络连接问题，推荐您使用内网服务MQ，如果在使用公网服务MQ时出现异常，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?accounttraceid=f7b76db740fa486baa4b63bd5848fbc1idrb)进行咨询。 |
|accessId|AccessKey ID|是|无|
|accessKey|AccessKey Secret|是|无|
|consumerGroup|订阅消费group名称|是|无|
|pullIntervalMs|拉取时间间隔|否|单位为毫秒。|
|startTime|消息消费启动的时间点|否|无|
|startMessageOffset|消息开始的偏移量|否|如果填写，将优先以startMessageoffset的位点开始加载。|
|tag|订阅的标签|否|无|
|lineDelimiter|解析block时行分隔符|否|默认值为 `\n`。|
|fieldDelimiter|字段分隔符|否|默认值为`\u0001` 。表示在只读模式下以`\u0001`（`\u0001`在只读模式不可见）作为分隔符，在编辑模式下以`^A`作为分隔符。|
|encoding|编码格式|否|默认值为`utf-8`。|
|lengthCheck|单行字段条数检查策略|否|默认值为NONE，表示： -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
-   解析出的字段数小于定义字段数时，跳过这行数据。

其它可选值为SKIP、EXCEPTION和PAD。 -   SKIP：解析出的字段数和定义字段数不同时跳过这行数据。
-   EXCEPTION：解析出的字段数和定义字段数不同时提示异常。
-   PAD：按从左到右顺序填充。
    -   解析出的字段数大于定义字段数时，按从左到右的顺序，取定义字段数量的数据。
    -   解析出的字段数小于定义字段数时，在行尾用null填充缺少的字段。 |
|columnErrorDebug|是否打开调试开关|否|默认值为FALSE。如果设置为TRUE，则打印解析异常的Log。|
|instanceID|MQ实例ID|否|如果MQ实例无独立命名空间，则不可以使用instanceID参数。如果MQ实例有独立命名空间，则instanceID参数必选。|

## 类型映射

|MQ字段类型|实时计算字段类型|
|------|--------|
|STRING|VARCHAR|

