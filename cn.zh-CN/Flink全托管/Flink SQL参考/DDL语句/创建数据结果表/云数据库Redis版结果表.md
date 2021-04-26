---
keyword: [Redis, 结果表]
---

# 云数据库Redis版结果表

本文为您介绍云数据库Redis版结果表DDL定义、WITH参数、类型映射和代码示例。

**说明：** 支持自建Redis服务。

## 什么是云数据库Redis版

阿里云数据库Redis版是兼容开源Redis协议标准、提供内存加硬盘混合存储的数据库服务，基于高可靠双机热备架构及可平滑扩展的集群架构，充分满足高吞吐、低延迟及弹性变配的业务需求。Flink支持将其作为流式数据的输出。

## DDL定义

云数据库Redis版结果表支持5种Redis数据结构，其DDL定义如下：

-   STRING类型

    DDL为两列：第1列为key，第2列为value。Redis插入数据的命令为`set key value`。

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      PRIMARY KEY (a) NOT ENFORCED -- 必填。
    ) with (
      'connector' = 'redis',
      'mode' = 'string',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   LIST类型

    DDL为两列：第1列为key，第2列为value。Redis插入数据的命令为`lpush key value`。

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      PRIMARY KEY (a) NOT ENFORCED -- 必填。
    ) with (
      'connector' = 'redis',
      'mode' = 'list',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   SET类型

    DDL为两列：第1列为key，第2列为value。Redis插入数据的命令为`sadd key value`。

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      PRIMARY KEY (a) NOT ENFORCED -- 必填。
    ) with (
      'connector' = 'redis',
      'mode' = 'set',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   HASHMAP类型

    DDL为三列：第1列为key，第2列为hash\_key，第3列为hash\_key对应的hash\_value。Redis插入数据的命令为`hmset key hash_key hash_value`。

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      c STRING,
      PRIMARY KEY (a) NOT ENFORCED -- 必填。
    ) with (
      'connector' = 'redis',
      'mode' = 'hashmap',
      'host' = '<yourHost>',
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>',
      'ignoreDelete' = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```

-   SORTEDSET类型

    DDL为三列：第1列为key，第2列为score，第3列为value。Redis插入数据的命令为`zadd key score value`。

    ```
    create table redis_sink (
      a STRING,
      b DOUBLE, --必须为DOUBLE类型。
      c STRING,
      PRIMARY KEY (a) NOT ENFORCED -- 必填。
    ) with (
      'connector' = 'redis',
      'mode' = 'sortedset',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- 收到Retraction时，是否删除已插入的数据，默认值为false。
    );
    ```


## WITH参数

|参数|说明|是否必填|取值|
|--|--|----|--|
|connector|结果表类型|是|固定值为`redis`。|
|mode|对应Redis的数据结构|取值如下： -   string
-   list
-   set
-   hashmap
-   sortedset |
|host|Redis Server连接地址|取值示例：`127.0.0.1`。|
|port|Redis Server连接端口|否|默认值为6379。|
|password|Redis密码|默认值为空，不进行权限验证。|
|dbNum|选择操作的数据库|默认值为0。|
|ignoreDelete|是否忽略Retraction消息。|默认值为false，可取值为true或false。如果设置为false，收到Retraction时，同时删除数据对应的key及已插入的数据。|
|clusterMode|Redis集群是否为cluster模式。|默认值为false。|

## 类型映射

|Redis字段类型|Flink字段类型|
|---------|---------|
|STRING|STRING|
|SCORE|DOUBLE|

**说明：** 因为Redis的SCORE类型应用于SORTEDSET（有序集合），所以需要手动为每个Value设置一个DOUBLE类型的SCORE，Value才能按照该SCORE从小到大进行排序。

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source (
  v STRING, 
  p STRING
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE redis_sink (
  a STRING,
  b STRING,
  PRIMARY KEY (a) NOT ENFORCED
) with (
  'connector' = 'redis',
  'mode' = 'string',
  'host' = '<yourHost>', 
  'port' = '<yourPort>', 
  'password' = '<yourPassword>'
);

INSERT INTO redis_sink 
SELECT v, p
FROM datagen_source;
```

