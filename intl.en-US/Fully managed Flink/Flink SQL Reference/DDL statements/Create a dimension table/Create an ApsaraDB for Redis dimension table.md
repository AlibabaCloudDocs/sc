---
keyword: [Redis, dimension table]
---

# Create an ApsaraDB for Redis dimension table

This topic describes how to create an ApsaraDB for Redis dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, field type mappings, and sample code used when you create an ApsaraDB for Redis dimension table.

**Note:**

-   ApsaraDB for Redis dimension tables can reference only the data of the STRING type in ApsaraDB for Redis databases.
-   You must declare only one primary key for each ApsaraDB for Redis dimension table.
-   You can declare only two fields for an ApsaraDB for Redis dimension table. The data type of the fields must be STRING.
-   When you join a dimension table with another table, the ON condition must contain equivalent conditions that include all primary keys.
-   You must set the cache parameter to None or LRU for an ApsaraDB for Redis dimension table.
-   Self-managed Redis databases are supported.

## Introduction to ApsaraDB for Redis

ApsaraDB for Redis is a database service that is compatible with the protocols of the open source Redis system. It supports a hybrid of memory and hard disks for storage. ApsaraDB for Redis provides a hot standby architecture to ensure high availability. Based on the scalable cluster architecture, ApsaraDB for Redis can meet the business requirements for high throughputs, low-latency operations, and flexible configuration changes.

## DDL syntax

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

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `redis`.|
|host|The endpoint of the ApsaraDB for Redis instance.|Yes|None.|
|port|The port that is used to access the ApsaraDB for Redis database.|No|Default value: 6379.|
|dbNum|The sequence number of the ApsaraDB for Redis database.|No|Default value: 0.|
|password|The password that is used to access the ApsaraDB for Redis database.|No|This parameter is empty by default. This indicates that permission verification is not required.|
|clusterMode|Specifies whether the ApsaraDB for Redis cluster is in cluster mode.|No|Default value: false.|
|hashName|The hash key name in hash mode.|No|This parameter is empty by default. In typical cases, the data type in the Redis dimension table is STRING, which is represented as `key-value` pairs. If you specify the hashName parameter, the ApsaraDB for Redis dimension table stores data of the HASHMAP data type in the format of `key-{field-value}` pairs. -   key is the value of the hashName parameter.
-   field is the value of the key parameter that you specify in the CREATE TABLE statement.
-   value is the value that is assigned to key. The value field has the same meaning as value in `key-value` for the STRING data type. |

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|Valid values for an ApsaraDB for Redis dimension table: -   None: indicates that no data is cached. This value is the default value.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters. |
|cacheSize|The maximum number of data records that can be cached.|No|This parameter is available only if you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period.|No|The cache does not expire by default. Unit: milliseconds. This parameter is available only if you set the cache parameter to LRU.|
|cacheEmpty|Specifies whether to cache empty results.|No|Default value: true.|

## Field type mapping

|Data type of ApsaraDB for Redis|Data type of Flink|
|-------------------------------|------------------|
|STRING|VARCHAR|

## Sample code

```
CREATE TEMPORARY TABLE datagen_source (
  id STRING, 
  data STRING,
  proctime as PROCTIME()
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE redis_dim (
  id STRING,
  name STRING,
  PRIMARY KEY (id) NOT ENFORCED -- The row key field in the ApsaraDB for Redis database.
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
SELECT e.*, w. *
FROM datagen_source AS e
JOIN redis_dim FOR SYSTEM_TIME AS OF e.proctime AS w
ON e.id = w.id;
```

