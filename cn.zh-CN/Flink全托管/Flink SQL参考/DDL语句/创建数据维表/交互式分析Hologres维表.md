---
keyword: [Hologres, 维表, 交互式分析]
---

# 交互式分析Hologres维表

本文为您介绍交互式分析Hologres维表DDL定义、WITH参数、CACHE参数和代码示例。

**说明：** Hologres Connector不支持访问Hologres外部表。Hologres外部表详情请参见[外部表](/cn.zh-CN/连接开发工具/HoloWeb/连接管理/MaxCompute加速/外部表.md)。

## 什么是交互式分析Hologres

[交互式分析Hologres](/cn.zh-CN/产品简介/什么是Hologres.md)兼容PostgreSQL协议，与大数据生态紧密连接，支持高并发、低延时实时分析处理PB级数据，让您轻松使用现有BI（Business Intelligence）工具对数据进行多维分析和业务探索。

## 使用限制

-   创建Hologres维表时建议选择行存模式，列存模式对于点查场景性能开销较大。

    选择行存模式创建维表时必须设置主键，并且将主键设置为clustering key才可以工作。示例语句如下：

    ```
    begin;
    create table test(a int primary key, b text, c text, d float8, e int8);
    call set_table_property('test', 'orientation', 'row');
    call set_table_property('test', 'clustering_key', 'a');
    commit;
    ```

-   Hologres维表的主键必须是Flink Join On的字段，Flink Join On的字段也必须是维表完整的主键字段，两者必须完全匹配。
-   Hologres Flink Connector的维表功能不支持一对多的输出。

## DDL定义

```
CREATE TABLE hologres_dim(
 id INT,
 len INT,
 content VARCHAR
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourUsername>',
  'password'='<yourPassword>',
  'endpoint'='<yourEndpoint>'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|维表类型。|是|固定值为`hologres`（小写）。|
|endPoint|Hologres服务地址。|是|详情请参见[访问域名](/cn.zh-CN/实例管理/访问域名.md)。|
|tablename|表名称。**说明：** 如果Schema不为Public时，则tableName需要填写为schema.tableName。

|是|无。|
|dbname|数据库名称。|是|无。|
|username|用户名，请填写阿里云账号的AccessKey ID。|是|无。|
|password|密码，请填写阿里云账号的AccessKey Secret。|否|无。|
|useRpcMode|自VVP 2.4.0版本开始，默认通过JDBC实现Hologres Connector。VVP 2.4.0以前版本默认是通过RPC实现Hologres Connector。|否|通过JDBC实现会占用SQL连接，导致JDBC链接数增加。如果您需要降低SQL连接数，则可以设置该参数值为true。**说明：** 仅VVP 2.4.0及以上版本支持该参数。 |
|connectionSize|单个Flink维表任务所创建的JDBC连接池大小。如果作业性能不足，建议您增加连接池大小。连接池大小和数据吞吐成正比。

|否|默认值为3，单位为个。**说明：** 仅VVP 2.4.0及以上版本支持该参数。 |

## CACHE参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|cache|缓存策略|否|Hologres仅支持以下两种缓存策略：-   None（默认值）：无缓存。
-   LRU：缓存维表里的部分数据。源表的每条数据都会触发系统先在Cache中查找数据，如果没有找到，则去物理维表中查找。

需要配置相关参数：缓存大小（cacheSize）和缓存更新时间间隔（cacheTTLMs）。 |
|cacheSize|缓存大小|否|选择`LRU`缓存策略后，可以设置缓存大小，默认值为10000行。|
|cacheTTLMs|缓存更新时间间隔|否|当选择LRU缓存策略后，可以设置缓存失效的超时时间，默认不过期。|
|async|是否异步同步数据|否|取值如下：-   true：表示异步同步数据。
-   false（默认值）：表示不进行异步同步数据。 |

## 类型映射

|Hologres字段类型|Flink字段类型|
|------------|---------|
|INT|INT|
|INT\[\]|ARRAY|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY|
|REAL|FLOAT|
|REAL\[\]|ARRAY|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY|
|NUMERIC|DECIMAL|
|DATE|DATE|
|TIMESTAMP WITH TIMEZONE|TIMESTAMP|

**说明：** 上表中未列出的Hologres类型，Connector还未支持转换。

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source (
   a INT,
   b BIGINT,
   c STRING,
   proctime AS PROCTIME()
) with (
   'connector' = 'datagen'
);

CREATE TEMPORARY TABLE hologres_dim (
   a INT, 
   b VARCHAR, 
   c VARCHAR
) with (
   'connector' = 'hologres',
   ...
);

CREATE TEMPORARY TABLE blackhole_sink (
   a INT,
   b STRING
) with (
   'connector' = 'blackhole'
);

insert into blackhole_sink select T.a,H.b
FROM datagen_source AS T JOIN hologres_dim FOR SYSTEM_TIME AS OF T.proctime AS H ON T.a = H.a;
```

