# RocketMQ接入点变更导致实时计算作业适配升级公告

因消息队列RocketMQ接入点地域化变更，如果您的实时计算作业使用了RocketMQ Connector，您需要在2021年11月之前升级您的实时计算作业至Blink 3.7.10或VVR 3.0.1及以上版本，并将作业中EndPoint参数取值更改为新的RocketMQ接入点，旧的RocketMQ接入点存在稳定性风险或不可用的问题。

## 升级公告

因消息队列RocketMQ接入点地域化变更（详情请参见[关于TCP内网接入点设置的公告]()），如果您已使用了Blink 3.7.10以下版本或VVR 3.0.1以下版本的RocketMQ Connector，则您需要将您的实时计算作业升级至Blink 3.7.10或VVR 3.0.1及以上版本，并将作业中EndPoint参数取值更改为新的RocketMQ接入点，EndPoint参数详情请参见：

-   Blink RocketMQ源表文档：[创建消息队列MQ源表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/创建消息队列MQ源表.md)。
-   Blink RocketMQ结果表文档：[创建消息队列MQ结果表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/创建消息队列MQ结果表.md)。
-   Flink RocketMQ源表文档：[消息队列RocketMQ版源表](/cn.zh-CN/Flink全托管/Flink SQL参考/DDL语句/创建数据源表/消息队列RocketMQ版源表.md)。
-   Flink RocketMQ结果表文档：[消息队列RocketMQ版结果表](/cn.zh-CN/Flink全托管/Flink SQL参考/DDL语句/创建数据结果表/消息队列RocketMQ版结果表.md)。

## 注意事项

-   旧的RocketMQ接入点在2011年11月后完全不可用，且使用旧的RocketMQ接入点的作业可能存在稳定性风险，因此请您尽快安排作业升级，务必在2021年11月之前完成升级工作。
-   RocketMQ产品承诺：2021年11月前不会下线旧的RocketMQ接入点，但旧的RocketMQ接入点无法保证作业稳定性。
-   本次升级会导致作业的State无法兼容，请结合业务情况合理安排升级时间，尽量减少对业务的影响。
-   2021年11月1日以后，实时计算Flink版产品侧不再对使用了旧的RocketMQ接入点的作业进行维护和支持。

