# Blink-3.6.0

本文为您介绍Blink 3.6.0版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

-   新增Connector：
    -   新增[创建分析型数据库PostgreSQL版结果表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/创建分析型数据库PostgreSQL版结果表.md)。
    -   新增[创建Phoenix5维表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据维表/创建Phoenix5维表.md)。
    -   新增[创建交互式分析Hologres源表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据源表/创建交互式分析Hologres源表.md)。
    -   新增[创建交互式分析Hologres维表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据维表/创建交互式分析Hologres维表.md)。
    -   新增[创建交互式分析Hologres结果表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/创建交互式分析Hologres结果表.md)。
-   新增功能：
    -   MQ结果表CSV格式支持写入KEY，二进制格式不支持写入KEY。
    -   支持Gemini 2.0（Gemini 增强版）。

## 主要缺陷修复

-   修复Kafka 08版本结果表，语法检查报错：`Kafka dont have sufficient arguments`。
-   修复OTS源表因为某个分区一直没有数据，线上运行源表节点导致的Failover：`java.lang.NullPointerException`。
-   修复OTS维表以注册存储方式引用，Token过期导致的Failover：`OTSNoPermissionAccess, [Message]:You have no permission to access the requested resource, please contact the resource owner`。

