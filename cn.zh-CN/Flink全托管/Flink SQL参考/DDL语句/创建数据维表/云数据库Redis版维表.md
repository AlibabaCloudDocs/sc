---
keyword: [Redis, 维表]
---

# 云数据库Redis版维表

本文为您介绍云数据库Redis维表DDL定义、WITH参数、CACHE参数、类型映射和代码示例。

**说明：**

-   Redis维表仅支持引用Redis数据存储中STRING类型的数据。
-   Redis维表必须声明且只能声明一个主键。
-   Redis维表仅支持声明两个字段，且字段类型必须为STRING。
-   维表JOIN时，ON条件必须包含所有主键的等值条件。
-   Redis仅支持None和LRU两种缓存策略。
-   支持自建Redis服务。

## 什么是云数据库Redis版

[阿里云数据库Redis版](/cn.zh-CN/产品简介/什么是云数据库Redis版.md)是兼容开源Redis协议标准、提供内存加硬盘混合存储的数据库服务，基于高可靠双机热备架构及可平滑扩展的集群架构，充分满足高吞吐、低延迟及弹性变配的业务需求。

## 前提条件

-   已创建云数据库Redis实例，详情请参见[步骤1：创建实例](/cn.zh-CN/快速入门/步骤1：创建实例.md)。
-   已设置白名单，详情请参见[步骤2：设置白名单](/cn.zh-CN/快速入门/步骤2：设置白名单.md)

## DDL定义

```
CREATE TABLE redis_dim (
  id STRING,
  name STRING,
  PRIMARY KEY (id) NOT ENFORCED
) WITH (
  'connector' = 'redis',
  'host' = '<yourHost>',
  'port' = '<yourPort>',
  'password' = '<yourPassword>',
  'dbNum' = '<yourDbNum>'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型。|是|固定值为`redis`。|
|host|Redis连接地址。|是|无。|
|port|Redis连接端口。|否|默认值为6379。|
|dbNum|选择操作的数据库。|否|默认值为0。|
|password|Redis密码。|否|默认值为空，不进行权限验证。|
|clusterMode|Redis集群是否为cluster模式。|否|取值如下：-   true：为cluster模式。
-   false（默认值）：不为cluster模式。 |
|hashName|Hash模式下的Hash Key名称。|否|默认值为空。通常，Redis维表中的数据类型为STRING类型，即`key-value`对。如果设置hashName参数，则Redis维表中的数据类型为HASHMAP类型，即`key-{field-value}`对，其中： -   key为hashName参数值。
-   field为您在CREATE TABLE中指明的key参数值。
-   value为key对应的赋值，和STRING类型`key-value`中value语义相同。 |

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略。|否|云数据库Redis维表支持以下两种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小。|否|选择LRU缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时长。|否|默认缓存不超时，单位为毫秒。可选LRU缓存策略，即设置缓存失效的超时时长。|
|cacheEmpty|是否缓存空结果。|否|默认值为true。|

## 类型映射

|Redis字段类型|Flink字段类型|
|---------|---------|
|STRING|VARCHAR|

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source (
  id STRING, 
  data STRING,
  proctime as PROCTIME()
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE redis_dim (
  id STRING,
  name STRING,
  PRIMARY KEY (id) NOT ENFORCED --Redis中的Row Key字段。
) WITH (
  'connector' = 'redis',
  'host' = '<yourHost>',
  'port' = '<yourPort>',
  'password' = '<yourPassword>'
);

CREATE TEMPORARY blackhole_sink (
  id STRING,
  data STRING,
  name STRING
) WITH (
  'connector' = 'blackhole'
);

INSERT INTO blackhole_sink
SELECT e.*, w.*
FROM datagen_source AS e
JOIN redis_dim FOR SYSTEM_TIME AS OF e.proctime AS w
ON e.id = w.id;
```

