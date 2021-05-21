# Blink-3.5.0-hotfix

本文为您介绍Blink 3.5.0版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

-   新增[创建InfluxDB结果表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/创建InfluxDB结果表.md)。
-   新增[创建Phoenix5结果表](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL参考/DDL语句/创建数据结果表/创建Phoenix5结果表.md)。
-   升级DataHub Connector SDK版本。

## 主要缺陷修复

-   修复使用全局ORDER BY但语法检查没有报错的缺陷。
-   修复底层VARCHAR类型隐式转换为整型，行为错误的缺陷。
-   修复因calcite bug，数据按照时间分组进行聚合，导致计算结果异常的缺陷。
-   修复语法检查DataHub源表产生`java.lang.NoClassDefFoundError: com/aliyun/datahub/client/auth/Account`报错的缺陷。

