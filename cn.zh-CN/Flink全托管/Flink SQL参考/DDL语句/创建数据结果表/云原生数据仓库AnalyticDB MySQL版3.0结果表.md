---
keyword: [云原生数据仓库AnalyticDB MySQL版, 3.0, 云原生数据仓库AnalyticDB MySQL版3.0, 结果表]
---

# 云原生数据仓库AnalyticDB MySQL版3.0结果表

本文为您介绍云原生数据仓库AnalyticDB MySQL版3.0结果表的DDL定义、WITH参数和类型映射。

## 什么是云原生数据仓库AnalyticDB MySQL版

[云原生数据仓库AnalyticDB MySQL版]()是融合数据库、大数据技术于一体的云原生企业级数据仓库服务。AnalyticDB MySQL版支持高吞吐的数据实时增删改、低延时的实时分析和复杂ETL，兼容上下游生态工具，可用于构建企业级报表系统、数据仓库和数据服务引擎。

## DDL定义

```
CREATE TABLE adb_sink (
  id INT,
  num BIGINT
) WITH (
  'connector' = 'adb3.0',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = 'jdbc:mysql://<yourNetworkAddress>:<PortId>/<yourDatabaseName>',
  'userName' = '<yourUsername>'
);
```

## WITH参数

|参数|注释说明|是否必选|备注|
|--|----|----|--|
|connector|结果表类型|是|固定值为`adb3.0`。|
|password|密码|是|无|
|tableName|名|是|无|
|url|jdbc连接地址|是|云原生数据仓库AnalyticDB MySQL版数据库地址。示例：`url='jdbc:mysql://databaseName****-cn-shenzhen-a.ads.aliyuncs.com:10014/databaseName'`。**说明：**

-   云原生数据仓库AnalyticDB MySQL版数据库连接信息，请参见[URL地址查询](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink SQL开发指南/数据存储/注册数据存储/注册云原生数据仓库AnalyticDB MySQL版.md)。
-   databaseName为云原生数据仓库AnalyticDB MySQL版数据库名称。 |
|username|用户名|是|无|
|maxRetryTimes|写入数据失败后，重试写入的次数|否|默认值为3。|
|batchSize|一次批量写入的条数|否|默认值为5000。|
|flushIntervalMs|清空缓存的时间间隔|否|单位为毫秒，默认值为0，即默认不开启缓存flush。如果设置值为 2000，表示如果缓存中的数据在等待2秒后，依然没有达到输出条件，系统会自动输出缓存中的所有数据。|

## 类型映射

|云原生数据仓库AnalyticDB MySQL版3.0字段类型|Flink字段类型|
|-------------------------------|---------|
|BOOLEAN|BOOLEAN|
|TINYINT|INT|
|SMALLINT|INT|
|INT|INT|
|BIGINT|BIGINT|
|DOUBLE|DOUBLE|
|VARCHAR|VARCHAR|
|DATETIME|TIMESTAMP|
|DATE|DATE|

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE adb_sink (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'adb3.0',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
);

INSERT INTO adb_sink
SELECT  * FROM datagen_source;
```

