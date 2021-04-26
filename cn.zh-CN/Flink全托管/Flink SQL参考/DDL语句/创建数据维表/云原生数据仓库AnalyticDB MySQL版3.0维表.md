---
keyword: [云原生数据仓库AnalyticDB MySQL版, 3.0, 云原生数据仓库AnalyticDB MySQL版3.0, 维表]
---

# 云原生数据仓库AnalyticDB MySQL版3.0维表

本文为您介绍云原生数据仓库AnalyticDB MySQL版3.0维表的DDL定义、WITH参数、CACHE参数和类型映射。

## DDL定义

```
CREATE TABLE adb30_dim (
    id1 INT,
    id2 VARCHAR
) WITH (
    'connector' = 'adb3.0',
    'password' = '<yourPassword>',
    'tableName' = '<yourTablename>',
    'url' = '<yourUrl>',
    'userName' = '<yourUsername>',
    'cache' = 'ALL',
    'cacheSize' = '500'
);
```

## WITH参数

|参数|注释说明|是否必选|备注|
|--|----|----|--|
|connector|维表类型|是|固定值为`adb3.0`。|
|password|密码|是|无|
|tableName|表名|是|无|
|url|URL地址|是|云原生数据仓库AnalyticDB MySQL版3.0的专有网络VPC地址。|
|username|用户名|是|无|
|maxRetryTimes|写入数据失败后，重试写入的次数|否|默认值为3。|
|CACHE|缓存策略、缓存大小和缓存超时时间|否|详情请参见[CACHE参数](#section_rd3_uc3_lqr)。|

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|云原生数据仓库AnalyticDB MySQL版3.0维表支持以下三种缓存策略： -   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。
-   ALL：缓存维表里的所有数据。在Job运行前，系统会将维表中所有数据加载到Cache中，之后所有的维表查找数据都会通过Cache进行。如果在Cache中无法找到数据，则KEY不存在，并在Cache过期后重新加载一遍全量Cache。

适用于远程表数据量小且MISS KEY（源表数据和维表JOIN时，ON条件无法关联）特别多的场景。


**说明：**

-   使用ALL或LRU缓存策略时，必须配置cacheSize参数。
-   如果使用CACHE ALL时，请注意节点内存大小，防止出现OOM。
-   因为系统会异步加载维表数据，所以在使用CACHE ALL时，需要增加维表JOIN节点的内存，增加的内存大小为远程表数据量的两倍。 |
|cacheSize|缓存大小，即缓存多少行数据。|否|cacheSize配置和cache有关：-   如果cache配置为None，则不用配置cacheSize参数（默认为空）。
-   如果cache配置为LRU，则必须配置cacheSize参数。
-   如果cache配置为ALL，则必须配置cacheSize参数。 |
|cacheTTLMs|缓存超时时间，单位为毫秒|否|cacheTTLMs配置和cache有关：-   如果cache配置为ALL，则cacheTTLMs为缓存加载的间隔时间，默认值为10000ms。
-   如果cache配置为LRU，则cacheTTLMs为缓存失效的超时时间，默认值为10000ms。 |

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
CREATE TEMPORARY TABLE datagen_source(
  a INT,
  b VARCHAR,
  c STRING,
  `proctime` AS PROCTIME()
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE adb_dim (
  a INT, 
  b VARCHAR, 
  c VARCHAR
) with (
  'connector' = 'adb3.0',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
);

CREATE TEMPORARY TABLE blackhole_sink(
  a INT,
  b VARCHAR
) with (
  'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN adb_dim FOR SYSTEM_TIME AS OF T.proctime AS H ON T.a = H.a;
```

