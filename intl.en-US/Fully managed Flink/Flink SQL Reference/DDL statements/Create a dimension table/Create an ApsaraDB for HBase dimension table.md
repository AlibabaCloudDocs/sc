---
keyword: [ApsaraDB for HBase, dimension table]
---

# Create an ApsaraDB for HBase dimension table

This topic describes how to create an ApsaraDB for HBase dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, and data type conversion involved when you create an ApsaraDB for HBase dimension table.

## DDL syntax

```
CREATE TABLE hbase_dim(
  rowkey INT,
  family1 ROW<q1 INT>,
  family2 ROW<q2 STRING, q3 BIGINT>,
  family3 ROW<q4 DOUBLE, q5 BOOLEAN, q6 STRING>
) with (
  'connector'='cloudhbase',
  'table-name'='<yourTableName>',
  'zookeeper.quorum'='<yourZookeeperQuorum>'
);
```

-   Column families of an ApsaraDB for HBase table must be declared as the ROW type. Each column family name is the field name of a row. In the DDL syntax, three column families family1, family2, and family3 are declared.
-   A column in a column family corresponds to a field in a row. The column name is the field name. In the DDL syntax, two columns q2 and q3 are declared in the column family family2.
-   In addition to the fields of the ROW type, only fields of the atomic type \(such as STRING and BIGINT\) can exist in an ApsaraDB for HBase table. The fields of the atomic type are considered as row keys of the table, for example, rowkey in the DDL statement.
-   The row key of an ApsaraDB for HBase table must be defined as the primary key of the result table. If no primary key is defined, the row key is used as the primary key.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `cloudhbase`.|
|table-name|The name of the ApsaraDB for HBase table.|Yes|None.|
|zookeeper.quorum|The URL that is used to access the ZooKeeper service of ApsaraDB for HBase.|Yes|None.|
|zookeeper.znode.parent|The root directory of ApsaraDB for HBase in the ZooKeeper service.|No|Default value: `/hbase`. **Note:** This parameter takes effect only in the ApsaraDB for HBase Standard Edition. |
|userName|The username that is used to access an ApsaraDB for HBase database.|No|**Note:** This parameter takes effect only in the ApsaraDB for HBase Enhanced Edition. |
|password|The password that is used to access an ApsaraDB for HBase database.|No|**Note:** This parameter takes effect only in the ApsaraDB for HBase Enhanced Edition. |
|haclient.cluster.id|The ID of an ApsaraDB for HBase instance in high availability \(HA\) mode.|No|This parameter is required only when you access zone-disaster recovery instances.**Note:** This parameter takes effect only in the ApsaraDB for HBase Enhanced Edition. |
|retries.number|The number of retries that are allowed for the ApsaraDB for HBase client to connect to the ApsaraDB for HBase database.|No|Default value: 31.|
|null-string-literal|If the field data type of ApsaraDB for HBase is STRING and the data of ApsaraDB for HBase is a byte array of the value of this parameter, the value of this parameter is null.|No|Default value: Null.|

## Parameters in the CACHE clause

|Parameter|Description|Required|Example|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|Valid values for an ApsaraDB for HBase dimension table: -   None: indicates that data is not cached.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters.

-   ALL: indicates that all data in the dimension table is cached. This is the default value. Before a job starts to run, the system loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, the system searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system loads all data in the cache again after cache entries expire.

If the data amount of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\)

If you use this cache policy, you must configure the cacheTTLMs and cacheReloadTimeBlackList parameters.


**Note:** If the cache parameter is set to ALL, you must increase the memory of the node for joining tables because the system asynchronously loads data from the dimension table. The increased memory size is two times that of the remote table. |
|cacheSize|The maximum number of data records that can be cached.|No|This parameter is available only if you set the cache parameter to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|No|-   If you set the cache parameter to LRU, this parameter specifies the interval at which the system refreshes the cache. By default, the cache is not refreshed.
-   If you set the cache parameter to ALL, this parameter specifies the interval at which the system refreshes the cache. By default, the cache is not refreshed. |
|cacheEmpty|Specifies whether to cache empty results.|No|Default value: true.|
|cacheReloadTimeBlackList|The time periods during which the cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. The cache is not refreshed during the time periods that you specified for this parameter. This parameter is useful for massive online promotional events such as Double 11.|No|This parameter is empty by default. The following example shows the format of the values: `2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`. The following list describes the delimiters that you must use for the parameter values: -   Separate time periods with commas \(`,`\).
-   Separate the start time and end time of each time period with a hyphen and a greater-than sign \(`->`\). |
|cacheScanLimit|The number of rows that the Remote Procedure Call Protocol \(RPC\) server returns to a client when the server reads full data from an ApsaraDB for HBase dimension table. This parameter is available only if the cache parameter is set to ALL.|No|Default value: 100.|

## Data type conversion

ApsaraDB for HBase data is converted to Flink data types by using org.apache.hadoop.hbase.util.Bytes. The decoding process varies based on the scenario:

-   If the Flink data type is the non-STRING type and a value in the ApsaraDB for HBase dimension table is an empty byte array, the value is decoded as null.
-   If the Flink data type is the STRING type and a value in the ApsaraDB for HBase dimension table is the byte array specified by null-string-literal, the value is decoded as null.

The following table lists the relationships between field data types of Flink and data type conversion functions of ApsaraDB for HBase.

|Data type of Flink|Data type conversion function of ApsaraDB for HBase|
|------------------|---------------------------------------------------|
|CHAR|String toString\(byte\[\] b\)|
|VARCHAR|
|STRING|
|BOOLEAN|boolean toBoolean\(byte\[\] b\)|
|BINARY|byte\[\]|
|VARBINARY|
|DECIMAL|BigDecimal toBigDecimal\(byte\[\] b\)|
|TINYINT|bytes\[0\]|
|SMALLINT|short toShort\(byte\[\] bytes\)|
|INT|int toInt\(byte\[\] bytes\)|
|BIGINT|long toLong\(byte\[\] bytes\)|
|FLOAT|float toFloat\(byte\[\] bytes\)|
|DOUBLE|double toDouble\(byte\[\] bytes\)|
|DATE|Converts a byte array of the ApsaraDB for HBase database into the INT data type by using int toInt\(byte\[\] bytes\). The value of the INT data type represents the number of days since January 1, 1970.|
|TIME|Converts a byte array of the ApsaraDB for HBase database into the INT data type by using int toInt\(byte\[\] bytes\). The value of the INT data type represents the number of milliseconds since 00:00:00.|
|TIMESTAMP|Converts a byte array of the ApsaraDB for HBase database into the LONG data type by using long toLong\(byte\[\] bytes\). The value of the LONG data type represents the number of milliseconds since 00:00:00 on January 1, 1970.|
|ARRAY|Not supported|
|MAP / MULTISET|Not supported|
|ROW|Not supported|

## Sample code

The following sample code demonstrates how to create an ApsaraDB for HBase dimension table in a Realtime Compute for Apache Flink job:

```
CREATE TABLE datagen_source (
  a INT,
  b BIGINT,
  c STRING,
  `proc_time` AS PROCTIME()
) with (
  'connector'='datagen'
);

CREATE TABLE hbase_dim (
  rowkey INT,
  family1 ROW<col1 INT>,
  family2 ROW<col1 STRING, col2 BIGINT>,
  family3 ROW<col1 DOUBLE, col2 BOOLEAN, col3 STRING>
) WITH (
  'connector' = 'cloudhbase',
  'table-name' = '<yourTableName>',
  'zookeeper.quorum' = '<yourZookeeperQuorum>'
);

CREATE TABLE blackhole_sink(
  a INT,
  f1c1 INT,
  f3c3 STRING
) with (
  'connector' = 'blackhole' 
);
  
INSERT INTO blackhole_sink
     SELECT a, family1.col1 as f1c1,  family3.col3 as f3c3 FROM datagen_source
JOIN hbase_dim FOR SYSTEM_TIME AS OF src.`proc_time` as h ON src.a = h.rowkey;
```

