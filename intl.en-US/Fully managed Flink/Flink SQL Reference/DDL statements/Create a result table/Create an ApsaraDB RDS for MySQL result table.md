---
keyword: [ApsaraDB RDS for MySQL, ApsaraDB RDS, MySQL]
---

# Create an ApsaraDB RDS for MySQL result table

This topic describes how to create an ApsaraDB RDS for MySQL result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, and field type mappings used when you create an ApsaraDB RDS for MySQL result table.

## DDL syntax

```
CREATE TABLE rds_sink (
   id INT,
   num BIGINT,
   PRIMARY KEY (id) NOT ENFORCED
) WITH (
   'connector' = 'rds',
   'password' = '<yourPassword>',
   'tableName' = '<yourTablename>',
   'url' = '<yourUrl>',
   'userName' = '<yourUsername>'
);
```

**Note:**

-   Flink converts each row of output data to a line of SQL statement and then writes and executes the statements in the destination ApsaraDB RDS or Distributed Relational Database Service \(DRDS\) database. If you want to write multiple rows of output data at a time, you must add `? rewriteBatchedStatements=true` to the end of the URL. This improves system performance.
-   You can define an auto-increment primary key for the ApsaraDB RDS for MySQL database that stores the result table. If you want to use the auto-increment primary key, you do not need to declare the auto-increment field in the DDL statement. For example, if you use ID as an auto-increment field, do not declare the ID field in the DDL statement. When a row of output data is written to the ApsaraDB RDS for MySQL database, the value for the auto-increment field is automatically filled.
-   If a DRDS result table is partitioned, the partition key must be declared in PRIMARY KEY\(\) of the DDL statement. Otherwise, you cannot write data to the partitioned table.
-   You must declare at least one non-primary key in the DDL statement. Otherwise, an error is returned.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `rds`.|
|password|The password that is used to access the database.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|url|The URL of the ApsaraDB RDS for MySQL database in a VPC.|Yes|For more information, see [View and change the internal and public endpoints and port numbers of an ApsaraDB RDS for MySQL instance](/intl.en-US/RDS MySQL Database/Database connection/View and change the internal and public endpoints and port numbers of an ApsaraDB
         RDS for MySQL instance.md).**Note:** If you want to write multiple rows of output data at a time, you must add `? rewriteBatchedStatements=true` to the end of the URL. This improves system performance when you write multiple rows of output data at a time. |
|userName|The username that is used to access the database.|Yes|None.|
|maxRetryTimes|The number of retries for writing data to the table after data writing fails.|No|Default value: 3.|
|batchSize|The number of data records that can be written at a time.|No|Default value: 5000.|
|flushIntervalMs|The time interval at which the cache is cleared.|No|Unit: milliseconds. Default value: 0. This value indicates that cache flushing is not enabled. If the value of this parameter is set to 2000, all cached data is automatically written to the result table when the number of input data records does not reach the value specified by the bufferSize parameter within 2,000 milliseconds.|

## Field type mapping

|Data type of ApsaraDB RDS for MySQL|Data type of Flink|
|-----------------------------------|------------------|
|BOOLEAN|BOOLEAN|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|TINYINT UNSIGNED|SMALLINT|
|INT|INT|
|SMALLINT UNSIGNED|INT|
|BIGINT|BIGINT|
|INT UNSIGNED|BIGINT|
|BIGINT UNSIGNED|DECIMAL\(20,0\)|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|VARBINARY|VARBINARY|

## Sample code

```
CREATE TEMPORARY TABLE datagen_source (
  `name` VARCHAR,
  `age` INT
) WITH (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE rds_sink (
  `name` VARCHAR,
  `age` INT
)WITH (
  'connector' = 'rds',
  'password' = '<yourPassword>',
  'tableName' = '<yourTablename>',
  'url' = '<yourUrl>',
  'userName' = '<yourUsername>'
);

INSERT INTO rds_sink
SELECT  * FROM datagen_source;
```

## FAQ

-   Q: When output data is written to an ApsaraDB RDS for MySQL result table, is a new data record inserted into the table or is the result table updated based on the primary key value?

    A: If a primary key is defined in the DDL statement, the following statement is executed to write the output data: `INSERT INTO tablename(field1,field2, field3, ...) VALUES(value1, value2, value3, ...) ON DUPLICATE KEY UPDATE field1=value1,field2=value2, field3=value3, ... ;`. If the primary key value in the output data already exists in the table, the matching record is updated. Otherwise, the output data is inserted as a new record. If no primary key is defined in the DDL statement, the `INSERT INTO` statement is executed to insert the output data.

-   Q: How do I perform GROUP BY operations based on the unique index of an ApsaraDB RDS for MySQL result table?

    A:

    -   You must declare the unique index in the GROUP BY clause in your Flink job.
    -   An ApsaraDB RDS for MySQL table has only one auto-increment primary key, which cannot be declared as a primary key in a Flink job.

