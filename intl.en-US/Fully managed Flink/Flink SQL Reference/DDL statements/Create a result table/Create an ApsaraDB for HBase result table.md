---
keyword: [HBase, result table]
---

# Create an ApsaraDB for HBase result table

This topic describes how to create an ApsaraDB for HBase result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, data type conversion, dynamic tables, and sample code used when you create an ApsaraDB for HBase result table.

## DDL syntax

```
CREATE TABLE hbase_sink(
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
-   You need only to declare the required column families and columns of an ApsaraDB for HBase table in the result table.
-   In addition to the fields of the ROW type, only fields of the atomic type \(such as STRING and BIGINT\) exist in an ApsaraDB for HBase table. The fields of the atomic type are considered as row keys of the table, for example, rowkey in the DDL statement.
-   The row key of an ApsaraDB for HBase table must be defined as the primary key of the result table. If no primary key is defined, the row key is used as the primary key.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `cloudhbase`.|
|table-name|The name of the ApsaraDB for HBase table.|Yes|None.|
|zookeeper.quorum|The URL that is used to access the ZooKeeper service of ApsaraDB for HBase.|Yes|None.|
|zookeeper.znode.parent|The root directory of ApsaraDB for HBase in the ZooKeeper service.|No|Default value: `/hbase`. **Note:** This parameter takes effect only in the ApsaraDB for HBase Standard Edition. |
|userName|The username that is used to access an ApsaraDB for HBase database.|No|**Note:** This parameter takes effect only in the ApsaraDB for HBase Enhanced Edition. |
|password|The password that is used to access an ApsaraDB for HBase database.|No|**Note:** This parameter takes effect only in the ApsaraDB for HBase Enhanced Edition. |
|haclient.cluster.id|The ID of an ApsaraDB for HBase instance in high availability \(HA\) mode.|No|This parameter is required only when you access zone-disaster recovery instances.**Note:** This parameter takes effect only in the ApsaraDB for HBase Enhanced Edition. |
|retries.number|The number of retries that are allowed for the ApsaraDB for HBase client to connect to the ApsaraDB for HBase database.|No|Default value: 31.|
|null-string-literal|If the field data type of ApsaraDB for HBase is STRING and the Flink field data is null, null-string-literal is assigned to the field and is written to the ApsaraDB for HBase database.|No|Default value: null.|
|sink.buffer-flush.max-size|The size of data in bytes cached in the memory before data is written to the ApsaraDB for HBase database. A large value of this parameter improves the write performance of ApsaraDB for HBase but prolongs the write latency and consumes more memory.|No|Default value: 2 MB. Unit: B, KB, MB, and GB. If this parameter is set to 0, no data is cached.|
|sink.buffer-flush.max-rows|The number of data records cached in the memory before data is written to the ApsaraDB for HBase database. A large value of this parameter improves the write performance of ApsaraDB for HBase but prolongs the write latency and consumes more memory.|No|Default value: 1000. If this parameter is set to 0, no data is cached.|
|sink.buffer-flush.interval|The interval at which cached data is written to the ApsaraDB for HBase database. This parameter controls the latency of data writing to the ApsaraDB for HBase database.|No|Default value: 1s. Unit: milliseconds, seconds, minutes, hours, and days. If this parameter is set to 0, periodic data writing is disabled.|

## Data type conversion

ApsaraDB for HBase data is converted to Flink data types by using org.apache.hadoop.hbase.util.Bytes. The decoding process varies based on the scenario:

-   Data of a non-string type, such as null, is encoded as an empty array.
-   Data of the STRING type is converted based on the value of null-string-literal.

The following table lists the relationships between the field data types of Flink and the data type conversion functions of ApsaraDB for HBase.

|Field data type of Flink|Data type conversion function of ApsaraDB for HBase|
|------------------------|---------------------------------------------------|
|CHAR|byte\[\] toBytes\(String s\)|
|VARCHAR|
|STRING|
|BOOLEAN|byte\[\] toBytes\(boolean b\)|
|BINARY|byte\[\]|
|VARBINARY|
|DECIMAL|byte\[\] toBytes\(BigDecimal v\)|
|TINYINT|new byte\[\] \{ val \}|
|SMALLINT|byte\[\] toBytes\(short val\)|
|INT|byte\[\] toBytes\(int val\)|
|BIGINT|byte\[\] toBytes\(long val\)|
|FLOAT|byte\[\] toBytes\(float val\)|
|DOUBLE|byte\[\] toBytes\(double val\)|
|DATE|Converts a date into the number of days represented by INT since January 1, 1970 and then into a byte array by using byte\[\] toBytes\(int val\).|
|TIME|Converts a time into the number of milliseconds represented by INT since 00:00:00 and then into a byte array by using byte\[\] toBytes\(int val\).|
|TIMESTAMP|Converts a timestamp into the number of milliseconds represented by LONG since 00:00:00 on January 1, 1970 and then into a byte array by using byte\[\] toBytes\(long val\).|
|ARRAY|Not supported|
|MAP|Not supported|
|MULTISET|
|ROW|Not supported|

## Dynamic table

Some result data of Flink is used as a dynamic column based on the value of a column and written to ApsaraDB for HBase. The following sample code shows that the hourly transaction amount of a product is used as a dynamic column.

```
CREATE TEMPORARY TABLE datagen_source (
    id INT,
    f1hour STRING,
    f1deal BIGINT,
    f2day STRING,
    f2deal BIGINT
) with (
    'connector'='datagen'
);

CREATE TEMPORARY TABLE hbase_sink (
    rowkey INT,
    f1 ROW<`hour` STRING, deal BIGINT>,
    f2 ROW<`day` STRING, deal BIGINT>
) with (
    'connector'='cloudhbase',
    'table-name'='<yourTableName>',
    'zookeeper.quorum'='<yourZookeeperQuorum>',
    'dynamic.table'='true'
);

INSERT INTO hbase_sink
SELECT id, ROW(f1hour, f1deal), ROW(f2day, f2deal) FROM datagen_source;
```

**Note:**

-   If dynamic.table is set to true, the table is an ApsaraDB for HBase table that supports dynamic columns.
-   Two fields must be declared in the rows that correspond to each column family. The value of the first field indicates the dynamic column, and the value of the second field indicates the value of the dynamic column.
-   If the src table contains the data record \(1, "10", 100, "2020-7-26", 10000\), the row that corresponds to the row key of 1 is inserted into the ApsaraDB for HBase table. The data record indicates that the product ID is 1, the transaction amount from 10:00 to 11:00 is 100, and the transaction amount on July 26, 2020 is 10,000. Based on the preceding statement, f1:10 is 100 and f2:2020-7-26 is 10000.

## Sample code

```
CREATE TEMPORARY TABLE datagen_source (
   rowkey INT,
   f1q1 INT,
   f2q1 STRING,
   f2q2 BIGINT,
   f3q1 DOUBLE,
   f3q2 BOOLEAN,
   f3q3 STRING
) with (
   'connector'='datagen'
);

CREATE TEMPORARY TABLE hbase_sink (
   rowkey INT,
   family1 ROW<q1 INT>,
   family2 ROW<q1 STRING, q2 BIGINT>,
   family3 ROW<q1 DOUBLE, q2 BOOLEAN, q3 STRING>,
   PRIMARY KEY (rowkey) NOT ENFORCE
) with (
   'connector'='cloudhbase',
   'table-name'='<yourTableName>',
   'zookeeper.quorum'='<yourZookeeperQuorum>'
);
 
INSERT INTO hbase_sink
SELECT rowkey, ROW(f1q1), ROW(f2q1, f2q2), ROW(f3q1, f3q2, f3q3) FROM datagen_source;
```

