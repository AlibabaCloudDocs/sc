# Overview

This topic describes how to use the standard CREATE TABLE statement to create a dimension table in Realtime Compute for Apache Flink. To use the standard data definition language \(DDL\) statement to create a dimension table, add PERIOD FOR SYSTEM\_TIME to the statement to define the change period of the dimension table.

## Example

```
CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id),
  PERIOD FOR SYSTEM_TIME  --Define the change period of the dimension table. In Realtime Compute for Apache Flink V3.X and later, you do not need to declare PERIOD FOR SYSTEM_TIME. You need to declare only FOR SYSTEM_TIME AS OF PROCTIME() when you join a dimension table with another table.
) with (
  type = 'RDS',
  ...
);
```

**Note:**

-   You must specify a primary key for each dimension table. When you join a dimension table with another table, the ON clause must contain the equivalent \(=\) conditions for all the primary key fields.
-   You can execute only the `INNER JOIN` or the `LEFT JOIN` statement to join a source table and a dimension table.
-   The unique key of the dimension table must be the same as that of the database table. If they are not the same, you may encounter the following issues:
    -   Reading data from the dimension table slows down.
    -   When you join the dimension table with another table, the join operation starts from the first data record. In the Realtime Compute for Apache Flink job, multiple data records that have the same key are sequentially updated in the database. This may cause errors in the join result.

## INDEX syntax

**Note:** We recommend that you use the `INDEX` syntax in Realtime Compute for Apache Flink V2.2.7 and later.

In Realtime Compute for Apache Flink versions that are earlier than V2.2, you must declare the `PRIMARY KEY` when you create a dimension table. In this case, you can perform only one-to-one table joins. The `INDEX` syntax is introduced to meet the requirements for one-to-many table joins. For dimension tables that do not support the `ALL` cache policy, you can use `INDEX LOOKUP` to meet the requirements for one-to-many table joins.

```
CREATE TABLE Persons (
    ID bigint,
    LastName varchar,
    FirstName varchar,
    Nick varchar,
    Age int,
    [UNIQUE] INDEX(LastName,FirstName,Nick), --Define the index. You do not need to specify the index type, such as fulltext or clustered.
    PERIOD FOR SYSTEM_TIME
) with (
  type='RDS',
  ...
);
```

`UNIQUE INDEX` represents a one-to-one table join. `INDEX` represents a one-to-many table join.

**Note:**

-   `UNIQUE CONSTRAINT` \(`UNIQUE KEY`\) is supported in Realtime Compute for Apache Flink V2.2.7 and later. In Realtime Compute for Apache Flink versions that are earlier than V2.2.7, you can define the index by using `PRIMARY KEY`.
-   The engine preferentially uses `UNIQUE INDEX` when it generates an execution plan. If INDEX is used in the DDL statement and the JOIN equivalent \(=\) conditions include both `UNIQUE` and `NON-UNIQUE INDEX`, the system preferentially uses `UNIQUE INDEX` to search for data in the right table.
-   The dimension table types, such as ApsaraDB RDS dimension table and MaxCompute dimension table, supports one-to-many table joins.
-   In one-to-many table joins, you can use the `maxJoinRows` parameter to specify the maximum number of associated rows in the right table for each row in the left table. The default value is 1024. If one row is associated with an excessively large number of rows, the performance of stream processing tasks may be compromised. If this occurs, you can increase the cache size. You can use the `cacheSize` parameter to limit the number of keys in the left table.
-   The INDEX syntax cannot be used to perform one-to-many table joins on Tablestore and Hologres dimension tables.

## Differences among dimension tables, source tables, and result tables

|Item|Source Table|Result table|Dimension table|
|----|------------|------------|---------------|
|Trigger computing|Supported|Not supported|Not supported|
|Read data|Supported \(You can directly read data from source tables.\)|Not supported|Supported \(You can read data from dimension tables only by joining a dimension table with a source table.\)|
|Write data|Not supported|Supported|Not supported|
|Cache data|Not supported|Not supported|Supported|

