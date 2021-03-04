---
keyword: [Redis, result table]
---

# Create an ApsaraDB for Redis result table

This topic describes how to create an ApsaraDB for Redis result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create an ApsaraDB for Redis result table.

**Note:** On-premises Redis databases are supported.

## Introduction to ApsaraDB for Redis

ApsaraDB for Redis is a database service that is compatible with the protocols of the open source Redis system. It supports a hybrid of memory and hard disks for storage. ApsaraDB for Redis provides a hot standby architecture to ensure high availability. Based on the scalable cluster architecture, ApsaraDB for Redis can meet the business requirements for high throughputs, low-latency operations, and flexible configuration changes. Flink can use the streaming data that is stored in ApsaraDB for Redis as output data.

## DDL syntax

You can use five data types when you write data to ApsaraDB for Redis result tables. To create ApsaraDB for Redis result tables, execute the following DDL statements:

-   STRING type

    A DDL statement has two columns. The first column is the key and the second column is the value. To insert data into an ApsaraDB for Redis result table, run the `set key value` command.

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      PRIMARY KEY (a) NOT ENFORCED -- Required.
    ) with (
      'connector' = 'redis',
      'mode' = 'string',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   LIST type

    A DDL statement has two columns. The first column is the key and the second column is the value. To insert data into an ApsaraDB for Redis result table, run the `lpush key value` command.

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      PRIMARY KEY (a) NOT ENFORCED -- Required.
    ) with (
      'connector' = 'redis',
      'mode' = 'list',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   SET type

    A DDL statement has two columns. The first column is the key and the second column is the value. To insert data into an ApsaraDB for Redis result table, run the `sadd key value` command.

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      PRIMARY KEY (a) NOT ENFORCED -- Required.
    ) with (
      'connector' = 'redis',
      'mode' = 'set',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   HASHMAP type

    A DDL statement has three columns. The first column is the key, the second column is the hash key, and the third column is the hash value. To insert data into an ApsaraDB for Redis result table, run the `hmset key hash_key hash_value` command.

    ```
    create table redis_sink (
      a STRING,
      b STRING,
      c STRING,
      PRIMARY KEY (a) NOT ENFORCED -- Required.
    ) with (
      'connector' = 'redis',
      'mode' = 'hashmap',
      'host' = '<yourHost>',
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>',
      'ignoreDelete' = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```

-   SORTEDSET type

    A DDL statement has three columns. The first column is the key, the second column is the score, and the third column is the value. To insert data into an ApsaraDB for Redis result table, run the `zadd key score value` command.

    ```
    create table redis_sink (
      a STRING,
      b DOUBLE, -- The data type must be DOUBLE.
      c STRING,
      PRIMARY KEY (a) NOT ENFORCED -- Required.
    ) with (
      'connector' = 'redis',
      'mode' = 'sortedset',
      'host' = '<yourHost>', 
      'port' = '<yourPort>', 
      'password' = '<yourPassword>',
      'dbNum' = '<yourDbNum>', 
      'ignoreDelete' = 'true' -- Specifies whether to delete the previously inserted data when the retraction message is returned. The default value is false.
    );
    ```


## Parameters in the WITH clause

|Parameter|Description|Required|Valid value|
|---------|-----------|--------|-----------|
|connector|The type of the result table.|Yes|Set the value to `redis`.|
|mode|The data structure of the ApsaraDB for Redis result table.|Valid values: -   string
-   list
-   set
-   hashmap
-   sortedset |
|host|The IP address that is used to access the ApsaraDB for Redis database.|Example: `127.0.0.1`.|
|port|The port that is used to access the ApsaraDB for Redis database.|No|Default value: 6379.|
|password|The password that is used to access the ApsaraDB for Redis database.|This parameter is empty by default. This indicates that permission verification is not required.|
|dbNum|The sequence number of the ApsaraDB for Redis database.|Default value: 0.|
|ignoreDelete|Specifies whether to ignore retraction messages.|Valid values: true and false. Default value: false. If this parameter is set to false, the inserted data and the keys of the data are deleted when a retraction message is received.|
|clusterMode|Specifies whether the ApsaraDB for Redis cluster is in cluster mode.|Default value: false.|

## Field type mapping

|Data type of ApsaraDB for Redis|Data type of Flink|
|-------------------------------|------------------|
|STRING|STRING|
|SCORE|DOUBLE|

**Note:** The data of the SCORE type is added to the values of the SORTEDSET data type in ApsaraDB for Redis databases. You must manually set a score of the DOUBLE type for each sorted set value and sort the values based on their scores in ascending order.

## Sample code

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

