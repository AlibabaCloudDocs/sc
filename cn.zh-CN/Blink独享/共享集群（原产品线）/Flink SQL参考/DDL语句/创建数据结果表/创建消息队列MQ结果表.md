---
keyword: [消息队列, 结果表, MQ]
---

# 创建消息队列MQ结果表

本文为您介绍如何创建实时计算Flink版消息队列MQ结果表，以及创建过程涉及到的WITH参数。

**说明：**

-   本文仅适用于Blink 1.4.5及以上版本。
-   如果您需要使用带独立命名空间的MQ，请使用Blink 3.x作业版本。

## 什么是消息列队MQ

消息队列MQ（Message Queue）是阿里云商用的专业消息中间件，是企业级互联网架构的核心产品。消息列队基于高可用分布式集群技术，搭建了包括发布订阅、消息轨迹、资源统计、定时（延时）和监控报警等一套完整的消息云服务。

## CSV格式

实时计算Flink版可以将消息队列作为流式数据进行输出，CSV格式输出示例如下。

```
CREATE TABLE stream_test_hotline_agent (
id INTEGER,
len BIGINT,
content VARCHAR
) WITH (
type='mq',
endpoint='<yourEndpoint>',
accessID='<yourAccessId>',
accessKey='<yourAccessSecret>',
topic='<yourTopicName>',
producerGroup='<yourGroupName>',
tag='<yourTagName>',
encoding='utf-8',
fieldDelimiter=',',
retryTimes='5',
sleepTimeMs='500'
);
```

## 二进制格式

实时计算Flink版可以将消息队列作为流式数据进行输出，二进制格式输出示例如下。

```
CREATE TABLE source_table (
  commodity VARCHAR
)WITH(
  type='random'
);

CREATE TABLE result_table (
  mess VARBINARY
) WITH (
  type = 'mq',
  endpoint='<yourEndpoint>',
  accessID='<yourAccessId>',
  accessKey='<yourAccessSecret>',
  topic='<yourTopicName>',
  producerGroup='<yourGroupName>'
);

INSERT INTO result_table
SELECT 
CAST(SUBSTRING(commodity,0,5) AS VARBINARY) AS mess   
FROM source_table;
```

**说明：** `cast(varchar as varbinary)`需要在Blink 2.0及以上版本使用。如果版本低于2.0，请先完成版本升级，详情请参见[管理独享集群Blink版本](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/管理独享集群Blink版本.md)。

## WITH参数

|参数|说明|备注|
|--|--|--|
|type|结果表类型|固定值为mq。|
|topic|Message Queue队列名称|无|
|endpoint|地址|阿里云消息队列提供内网服务MQ（非公网region）和公网服务MQ（公网region）两种类型，请务必根据您购买的MQ的类型选择对应正确的接入地址：-   Blink 3.7.10及以上版本的作业，需要使用TCP协议客户端接入点，详情请参见 [关于TCP内网接入点设置的公告]()。接入点获取方式如下：
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

-   内网服务无法跨地域访问。例如，您所购买的实时计算Flink版服务的地域为华东1，但是购买的消息队列MQ服务的地域为华东2，则无法访问。
-   独享集群默认不能访问公网，如果需要请配置NAT网关，详情参见[独享集群如何访问公网？](/cn.zh-CN/Blink独享/共享集群（原产品线）/常见问题/如何使用.md)
-   由于阿里云网络安全策略动态变化，实时计算Flink版连接公网服务MQ时可能会出现网络连接问题，推荐您使用内网服务MQ。如果在使用公网服务MQ时出现异常，请您[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex?accounttraceid=f7b76db740fa486baa4b63bd5848fbc1idrb)进行咨询。 |
|accessID|AccessKey ID|无|
|accessKey|AccessKey Secret|无|
|producerGroup|写入的群组|无|
|tag|写入的标签|可选，默认值为空。|
|fieldDelimiter|字段分割符|可选，默认值为`\u0001` 。分隔符的使用情况如下所示： -   只读模式：以 `\u0001`作为分隔符，`\u0001`在只读模式不可见。
-   编辑模式：以`^A`作为分隔符。 |
|encoding|编码类型|可选，默认值为`utf-8`。|
|retryTimes|写入的重试次数|可选，默认值为10。|
|sleepTimeMs|重试间隔时间|可选，默认值为1000（毫秒）。|
|instanceID|MQ实例ID|-   如果MQ实例无独立命名空间，则不可以使用instanceID参数。
-   如果MQ实例有独立命名空间，则instanceID参数必选。 |

