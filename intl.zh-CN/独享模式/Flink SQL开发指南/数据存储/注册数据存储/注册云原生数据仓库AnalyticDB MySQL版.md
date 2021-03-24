---
keyword: [分析型数据库, MySQL, 云原生数据仓库AnalyticDB MySQL版]
---

# 注册云原生数据仓库AnalyticDB MySQL版

本文为您介绍注册云原生数据仓库AnalyticDB MySQL版数据存储所需的参数信息。

**说明：** 本文档仅适用于云原生数据仓库AnalyticDB MySQL版 2.0版本。云原生数据仓库AnalyticDB MySQL版 3.0版本暂不支持结果表注册存储功能，请使用明文方式进行注册，详情请参见[创建云原生数据仓库AnalyticDB MySQL版3.0结果表](/intl.zh-CN/独享模式/Flink SQL参考/DDL语句/创建数据结果表/创建云原生数据仓库AnalyticDB MySQL版3.0结果表.md)。

## 注册存储

**说明：** 使用注册存储功能前，请先完成实时计算Flink版对存储设备的访问授权，授权方法请您参见[独享模式角色授权](/intl.zh-CN/独享模式/准备工作/角色授权/独享模式角色授权.md)

1.  登录[实时计算控制台](https://stream-ap-southeast-3.console.aliyun.com)。
2.  在页面顶部，单击**开发**。
3.  在左侧导航栏，单击**数据存储**。
4.  在页面左上角，单击**注册与网络**。
5.  在**注册数据存储与网络探测**对话框，配置存储设备参数。
6.  单击**注册**。

## 参数说明

|参数名称|说明|
|----|--|
|URL|云原生数据仓库AnalyticDB MySQL版控制台链接信息。分为以下两种情况： -   共享模式：请使用云原生数据仓库AnalyticDB MySQL版的经典网络URL。
-   独享模式：请使用云原生数据仓库AnalyticDB MySQL版的VPC网络URL。

URL​地址查询步骤如下：

1.  登录[AnalyticDB 控制台](https://ads.console.aliyun.com/?spm=a2c4g.11186623.2.23.2c952b809T8asM)。
2.  单击对应的集群名称，进入**基本信息**页面。
3.  在**网络信息**中查看相应的连接地址。 |
|Database|云原生数据仓库AnalyticDB MySQL版数据库名称，即云原生数据仓库AnalyticDB MySQL版实例名称。|
|AccessKey ID|阿里云账号的AccessKey ID。|
|AccessKey Secret|阿里云账号的AccessKey Secret。|

