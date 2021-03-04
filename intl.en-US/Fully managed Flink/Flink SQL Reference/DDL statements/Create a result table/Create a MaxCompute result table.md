---
keyword: [result table, MaxCompute]
---

# Create a MaxCompute result table

This topic describes how to create a MaxCompute result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a MaxCompute result table.

## Principles

The MaxCompute sink works in two phases:

1.  Writes data. The MaxCompute sink calls an API operation in the MaxCompute SDK to write data to the buffer. Then, the sink uploads data to the temporary files of MaxCompute at a specified interval or when the data size in the buffer exceeds 64 MB.
2.  Commits sessions. When a task creates checkpoints, the MaxCompute sink calls the Tunnel commit method to commit sessions and moves temporary files to the data directory of the MaxCompute table. Then, the MaxCompute sink modifies the metadata.

    **Note:** The commit method does not provide atomicity. Therefore, the MaxCompute sink supports at-least-once delivery instead of exactly-once delivery.


## DDL syntax

Flink allows you to use MaxCompute to store output data. The following code shows an example:

```
create table odps_sink(
    id INT,
    user_name VARCHAR,
    content VARCHAR
) with (
    'connector' = 'odps',
    'endpoint' = '<yourEndpoint>',
    'tunnelEndpoint' = '<yourTunnelEndpoint>',
    'project' = '<yourProjectName>',
    'tablename' = '<yourTableName>',
    'accessid' = '<yourAccessKeyId>',
    'accesskey' = '<yourAccessKeySecret>',
    'partition' = 'ds=2018****'
);
```

**Note:**

-   The parameters in the WITH clause must be all lowercase.
-   The name, sequence, and type of fields in the DDL statement must be the same as those in the MaxCompute physical table. Otherwise, the data that is queried in the MaxCompute physical table may be `/n`.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `odps`.|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|project|The name of a MaxCompute project.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|partition|The name of a partition.|No|This parameter is required if a partitioned table is used. Take note of the following points:-   Fixed partition

For example, ``partition`='ds=20180905'` indicates that data is written to the `ds=20180905` partition.

-   Dynamic partition

If partition values are not explicitly specified, data is written to the related partitions based on the values of the specified partition key columns. For example, ``partition`='ds'` indicates that data is written to a specified partition based on the value of the `ds` field.

If you want to create multi-level dynamic partitions, you must make sure that the fields in the partitions are in the same order as those in the MaxCompute physical table. Multiple fields in the partitions are separated by commas \(,\).

**Note:**

    -   In the CREATE TABLE statement, you must explicitly specify the partition key column that you use to create dynamic partitions.
    -   If the dynamic partition key column is empty and `ds=null` or `ds=''` exists in the data source, the output is the partition with ds=NULL. |
|flushIntervalMs|The flush interval for the buffer of a writer in MaxCompute Tunnel. The MaxCompute sink first inserts data into its buffer. Then, the MaxCompute sink writes the data in the buffer to the destination MaxCompute table at an interval that is specified by the flushIntervalMs parameter. The sink also writes the data to the destination table when the size of the buffer data exceeds the specified threshold.

|No|Default value: 30000. Unit: milliseconds.|
|partitionBy|The columns based on which hash shuffling is implemented. Before data is written to a sink node, the system implements hash shuffling based on the value of this parameter. This way, data flows to the related sink nodes.|No|The system implements hash shuffling based on multiple columns. The column names are separated by commas \(,\). This parameter is empty by default.|
|dynamicPartitionLimit|The maximum number of partitions.|No|Default value: 100. The system maintains the mappings between the existing partitions to which data is written and the TunnelBufferedWriter in a map. If the map size exceeds the value of the dynamicPartitionLimit parameter, the system reports the following error: `Too many dynamic partitions: 100, which exceeds the size limit: 100`.|

## Field type mapping

|Data type of MaxCompute|Data type of Flink|
|-----------------------|------------------|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DATETIME|TIMESTAMP|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|STRING|VARCHAR|
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|

## Sample code

-   Write data to a fixed partition

    ```
    CREATE TEMPORARY TABLE datagen_source (
        id INT,
        len INT,
        content VARCHAR
    ) with (
        'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE odps_sink (
        id INT,
        len INT,
        content VARCHAR
    ) with (
        'connector' = 'odps',
        'endpoint' = '<yourEndpoint>',
        'tunnelEndpoint' = '<yourTunnelEndpoint>',
        'project' = '<yourProjectName>',
        'tablename' = '<yourTableName>',
        'accessid' = '<yourAccessKeyId>',
        'accesskey' = '<yourAccessKeySecret>',
        'partition' = 'ds=20180905'
    );
    
    INSERT INTO odps_sink 
    SELECT 
         id, len, content 
    FROM datagen_source;
    ```

-   Write data to a dynamic partition

    ```
    CREATE TEMPORARY TABLE datagen_source (
        id INT,
        len INT,
        content VARCHAR,
        c TIMESTAMP 
    ) with (
        'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE odps_sink (
         id  INT,
         len INT,
         content VARCHAR,
         ds VARCHAR --The partition key column that you use to create dynamic partitions must be explicitly specified.
    ) with (
         'connector' = 'odps',
         'endpoint' = '<yourEndpoint>',
         'tunnelEndpoint' = '<yourTunnelEndpoint>',
         'project' = '<yourProjectName>',
         'tablename' = '<yourTableName>',
         'accessid' = '<yourAccessKeyId>',
         'accesskey' = '<yourAccessKeySecret>',
         `partition`='ds' --The partition value is not provided. This means that data is written to a partition specified by the ds field.
    );
    
    INSERT INTO odps_sink 
    SELECT 
          id, 
          len, 
          content,
          DATE_FORMAT(c, 'yyMMdd') as ds
    FROM datagen_source;
    ```


