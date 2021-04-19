# Blink-3.7.10

本文为您介绍Blink 3.7.10版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

消息队列RocketMQ Connector升级底层客户端依赖版本，因此所有的type=mq的DDL需要根据RocketMQ接入点的变更，调整Endpoint参数取值至RocketMQ的TCP内网接入点，详情请参见[关于TCP内网接入点设置的公告]()。

## 主要缺陷修复

修复AutoScale的缺陷。

