---
keyword: [incremental MaxCompute, source table]
---

# Create an incremental MaxCompute source table

This topic describes how to create an incremental MaxCompute source table. It also describes data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and FAQ used when you create an incremental MaxCompute source table.

## Introduction to MaxCompute

MaxCompute is a fast and fully-managed computing platform for large-scale data warehousing. MaxCompute can process exabytes of data. MaxCompute provides you with solutions for storing and computing mass structured data in data warehouses and analytics and modeling services. For more information about MaxCompute, see [What is MaxCompute?](/intl.en-US/Product Introduction/What is MaxCompute?.md).

## Prerequisites

A MaxCompute table is created. For more information about how to create a MaxCompute table, see [Create tables](/intl.en-US/Quick Start/Use MaxCompute through the MaxCompute client/Create and view a table.md).

## DDL syntax

```
create table odps_source(
  id INT,
  user_name VARCHAR,
  content VARCHAR
) with (
  'connector' = 'continuous-odps', 
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'startpartition' = 'ds=2018****'
);
```

**Note:**

-   The parameters in the WITH clause must be all lowercase.
-   Incremental MaxCompute source tables cannot be used as dimension tables.
-   Incremental MaxCompute source tables must be partitioned tables.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `continuous-odps`.|
|endPoint|The endpoint of the MaxCompute service.|Yes|For more information, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|Yes|For more information, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|project|The name of the project to which the MaxCompute table belongs.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|accessId|The AccessKey ID of your Alibaba Cloud account.|Yes|None.|
|accessKey|The AccessKey secret of your Alibaba Cloud account.|Yes|None.|
|startPartition|The partition in which data starts to be read. When the system loads partitioned tables, it compares startPartition and all partitions in each partitioned table in alphabetical order and then loads the data that meets the specified condition from the partitions. An incremental MaxCompute source table can also continuously listen to incremental MaxCompute partitioned tables. After the source table reads data from all the existing partitions, it continues to listen to the generation of new partitions. After a new partition is generated, the source table reads data from the new partition.

**Note:**

-   An incremental MaxCompute source table must have hash partitions. Subpartitions are optional.
-   If you specify a subpartition, make sure that it is placed after a hash partition.
-   If the partition specified by the startPartition parameter does not exist, the system reads data from the next partition.

|Yes|For example, if startPartition is set to ds=20191201, data of all the partitions that meet the condition of ds \>= 20191201 in the incremental MaxCompute partitioned table is loaded. For example, an incremental MaxCompute partitioned table has five partitions, each partition has two partition key columns with the ds hash partition and the type subpartition.

-   ds=20191201,type=a
-   ds=20191201,type=b
-   ds=20191202,type=a
-   ds=20191202,type=b
-   ds=20191202,type=c

If the values of startPartition for the partitions are different, the following partitions meet the preceding condition:

-   ds=20191202
    -   ds=20191202,type=a
    -   ds=20191202,type=b
    -   ds=20191202,type=c
-   ds=20191201,type=c
    -   ds=20191202,type=a
    -   ds=20191202,type=b
    -   ds=20191202,type=c |

## Mapping between field data types

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
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|
|STRING|VARCHAR|

**Note:**

-   Incremental MaxCompute source tables do not support the CHAR, VARCHAR, ARRAY, MAP, or STRUCT data types.
-   You can use the STRING data type instead of the VARCHAR data type.

## Sample code

One partition is generated in an incremental MaxCompute source table every day. The partition key column is ds. The incremental MaxCompute source table loads data from partitions whose numbers are greater than or equal to 20191201 and continuously listens to the generation of new partitions.

```
-- The incremental MaxCompute source table reads data from partitions in the range of [ds=20191201, ∞). 
CREATE TEMPORARY TABLE odps_source (
  cid VARCHAR,
  rt DOUBLE
) with (
  'connector' = 'continuous-odps', 
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'startpartition' = 'ds=2018****'
);

CREATE TEMPORARY TABLE blackhole_sink (
  cid VARCHAR,
  rt DOUBLE
) WITH (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink 
SELECT 
    cid, rt FROM odps_source;
```

## FAQ

-   Q: What do the endPoint and tunnelEndpoint parameters mean? What happens if the two parameters are incorrectly configured?

    A: For more information about the endPoint and tunnelEndpoint parameters, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md). If the two parameters are incorrectly configured in a VPC, a task error may occur.

    -   If the endPoint parameter is incorrectly configured, the task stops at a progress of 91%.
    -   If the tunnelEndpoint parameter is incorrectly configured, the task fails.
-   Q: How does the incremental MaxCompute data storage read data from an incremental MaxCompute source table?

    A: The incremental MaxCompute data storage reads incremental MaxCompute data by using a tunnel. Therefore, the read speed and bandwidth are restricted by the bandwidth of the tunnel used by the incremental MaxCompute source table.

-   Q: Can the data that is appended to the incremental MaxCompute source table or the partitions of the table be read after a Flink job is started?

    A: No, the new data cannot be read. The incremental MaxCompute data storage reads data from tables or partitions by using a tunnel. After a Flink job is started, the reader exits after data reading is complete. Then, the reader does not read new data from the incremental MaxCompute source table or the partitions of the table.

-   Q: How do I view the partition names of an incremental MaxCompute source table?

    A: You can perform the following steps to view the partition names of an incremental MaxCompute source table on the table details page:

    1.  Go to the [Data Map](https://meta.dw.alibaba-inc.com/store/index.html) page to search for the table name.
    2.  Click the table name.
    3.  On the right side of the table details page, click the **Details** tab and then the **Partitions** tab. You can view MaxCompute partition names in the **Partition Name** column.
-   Q: If an incremental MaxCompute source table is referenced as a data source, can the data that is appended to an existing partition or table be read after a job is started?

    A: No, the new data cannot be read, and the job may fail. The MaxCompute data storage uses `ODPS DOWNLOAD SESSION` to read data from tables or partitions. When you create a `DOWNLOAD SESSION`, the MaxCompute server creates an index file, which contains the data mapping obtained when the `DOWNLOAD SESSION` is created. Subsequent data reading is performed based on the data mapping. Therefore, the data that is appended to the partitions after `DOWNLOAD SESSION` is created cannot be read in normal cases. This issue occurs in the following scenarios:

    -   If the MaxCompute data storage reads data by using a tunnel, the following error is returned when new data is written to a partition: `ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`
    -   New data is written to a partition. However, the tunnel through which data is read is disabled Therefore, the new data cannot be read. If a job is recovered from failure or is resumed, the data may not be correct. For example, existing data is read again but the newly added data may not be read.
-   Q: Can I suspend and resume a job for an incremental MaxCompute source table? Can I also change the parallelism of the incremental MaxCompute source table?

    A: No, you cannot suspend or resume a job for an incremental MaxCompute source table, nor can you change the parallelism of the incremental MaxCompute source table. MaxCompute determines which data in which partitions need to be read for each parallel job and records the consumption information for each concurrent job in the state based on the parallelism. This way, MaxCompute can continue reading data from the last read position after a job is suspended and then resumed or fails.

    If you suspend and then resume a job for an incremental MaxCompute source table after you change the parallelism of the source table, the impact on the job cannot be estimated because some data may be read repeatedly while some data may not be read.

-   Q: What does an incremental MaxCompute source table do if some data is not written to a newly detected partition?

    A: No mechanism is available to determine whether data in a partition is complete. If an incremental MaxCompute source table detects a new partition, it reads data from the partition. If the incremental MaxCompute source table reads MaxCompute partitioned table T with the partition key column of ds, data in table T is written in the following methods:

    -   Not recommended: Create a partition, such as ds=20191010 and then write data to it. If the incremental MaxCompute source table consumes table T and detects the new partition ds=20191010, it reads data from the new partition immediately. If not all the data is not written to the partition, the data fails to be read.
    -   Recommended: Execute the Insert overwrite table T partition \(ds='20191010'\) ... statement without the need to create a partition.After the job succeeds, both the partition and data are displayed.

