# Create an incremental MaxCompute source table

This topic describes how to create an incremental MaxCompute source table in Realtime Compute for Apache Flink. It also describes the parameters in the WITH clause, data type mapping, and FAQ involved when you create such a source table.

**Note:**

-   This topic applies only to Blink 3.5.0-hotfix.
-   Incremental MaxCompute source tables cannot be used as dimension tables.
-   Incremental MaxCompute source tables support only partitioned tables.

## Syntax

```
create table odps_source(
    id int,
    user_name VARCHAR,
    content VARCHAR
) with (
    type = 'continuous-odps',
    endPoint = 'your_end_point_name',
    project = 'your_project_name',
    tableName = 'your_table_name',
    accessId = 'your_access_id',
    accessKey = 'your_access_key',
    startPartition = 'ds=20180905'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|type|The type of the connector.|Yes|Set the value to `continuous-odps`.|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|-   Yes: This parameter is required if MaxCompute is deployed in a VPC.
-   No: This parameter is not required if MaxCompute is not deployed in a VPC.

|For more information, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|project|The name of the project to which the table belongs.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|startPartition|The partition in which data starts to be read. When the system loads partitioned tables, it compares startPartition and all partitions in each partitioned table in alphabetical order and then loads the data that meets a specified condition from the partitions. An incremental MaxCompute source table can also continuously listen to incremental MaxCompute partitioned tables. After the source table reads data from all the existing partitions, it continues to listen to the generation of new partitions. If a new partition is generated, the source table reads data from the new partition.

**Note:**

-   An incremental MaxCompute source table must have level-1 partitions. Level-2 partitions are optional.
-   If you specify a level-2 partition, make sure that it is placed after a level-1 partition.
-   If the partition specified by the startPartition parameter does not exist, the system reads data from the next partition.

|Yes|For example, if startPartition is set to ds=20191201, data of all partitions that meets the condition of ds \>= 20191201 in the incremental MaxCompute partitioned table is loaded.Assume that an incremental MaxCompute partitioned table contains five partitions. Each partition has two partition key columns with a level-1 partition of ds and level-2 partition of type.

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

## Field type mapping

|MaxCompute data type|Data type of Realtime Compute for Apache Flink|
|--------------------|----------------------------------------------|
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
|STRING|ARCHAR|

**Note:**

-   Incremental MaxCompute source tables do not support the CHAR, VARCHAR, ARRAY, MAP, or STRUCT data types.
-   You can use the STRING data type instead of the VARCHAR data type.

## Sample code

One partition is generated in an incremental MaxCompute source table every day. The partition key column is ds. The incremental MaxCompute source table loads data from partitions whose numbers are greater than or equal to 20191201 and continuously listens to the generation of new partitions.

```
-- The incremental MaxCompute source table reads data from partitions in the range of [ds=20191201, ∞).
CREATE TABLE odps_source (
    cid VARCHAR,
    rt DOUBLE,
) with (
    type = 'continuous-odps', 
    endPoint = 'your_end_point_name', 
    project = 'your_project_name',
    tableName = 'your_table_name',
    accessId = 'xxxx',
    accessKey = 'xxxx',
    startPartition = 'ds=20191201'
);

CREATE TABLE test (
    cid VARCHAR,
    rt DOUBLE,
) with (
    type='print'
);

INSERT INTO test 
SELECT 
    cid, rt FROM odps_source;
```

## FAQ

-   Q: What do I do if the values of the endPoint and tunnelEndpont parameters in the DDL statement are incorrect?

    A: For more information about the endPoint and tunnelEndpont parameters, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.mdsection_f2d_51y_5db). Incorrect configuration of parameters may lead to the following issues:

    -   If the configuration of the endPoint parameter is incorrect, the task publish progress stops at 91%.
    -   If the configuration of the tunnelEndpont parameter is incorrect, the task fails.
-   Q: How does the incremental MaxCompute data storage read data in an incremental MaxCompute source table?

    A: The incremental MaxCompute data storage reads MaxCompute data by using a tunnel. Therefore, the read speed and bandwidth are restricted by the bandwidth of the tunnel used by the incremental MaxCompute source table.

-   Q: If data of some partitions of an incremental MaxCompute source table has been read, can the incremental MaxCompute data storage read data that is newly written into these partitions after a Realtime Compute for Apache Flink job is started?

    A: No, the incremental MaxCompute data storage cannot read the new data from the partitions. The incremental MaxCompute data storage reads data from the partitions of an incremental MaxCompute source table by using a tunnel. After a Realtime Compute for Apache Flink job is started, MaxCompute Reader exits when data reading is completed. Then, MaxCompute Reader does not read new data from the partitions of the incremental MaxCompute source table.

-   Q: How do I view partition names of an incremental MaxCompute source table?

    A: You can perform the following steps to view the partition names of an incremental MaxCompute source table on the table details page:

    1.  Go to the [Data Map](https://meta.dw.alibaba-inc.com/store/index.html) page to search for the required table name.
    2.  Click the table name.
    3.  On the right side of the table details page, click the **Details** tab and then the **Partitions** tab. You can view MaxCompute partition names in the **Partition Name** column.
-   Q: If an incremental MaxCompute source table is referenced as a data source, can the data that is appended to an existing partition be read after a job is started?

    A: No, the new data cannot be read, and the job may fail. The MaxCompute data storage uses `ODPS DOWNLOAD SESSION` to read data from tables or partitions. When you create a `DOWNLOAD SESSION`, the MaxCompute server creates an index file, which contains the data mapping obtained when the `DOWNLOAD SESSION` is created. Subsequent data reading is performed based on the data mapping. Therefore, the data that is appended to the partitions after `DOWNLOAD SESSION` is created cannot be read in normal cases. This issue occurs in the following scenarios:

    -   When the MaxCompute data storage reads data by using a tunnel, the following error is reported if new data is written into a partition: `ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`
    -   New data is written into a partition. However, the tunnel through which data is read is disabled. Therefore, the new data cannot be read. If a job fails or is resumed, the data may not be correct. For example, existing data is read again but the newly added data may not be read.
-   Q: Can I suspend and resume a job for an incremental MaxCompute source table? Can I also change the parallelism of the incremental MaxCompute source table?

    A: No, you cannot suspend or resume a job for an incremental MaxCompute source table, or change the parallelism of the incremental MaxCompute source table. MaxCompute determines which data in which partitions need to be read for each concurrent job and records the consumption information for each concurrent job in the state based on the parallelism. This way, MaxCompute can continue reading data from the last read position after the job is suspended and then resumed or fails.

    If you suspend and then resume a job for an incremental MaxCompute source table after you change the parallelism of the source table, the impact on the job cannot be estimated because some data may be read repeatedly but some data may not be read.

-   Q: What does an incremental MaxCompute source table do if it finds that some data is not written into the new partition it detects?

    A: No mechanism is available to determine whether data in a partition is complete. If an incremental MaxCompute source table detects a new partition, it reads data from the partition. Assume that the incremental MaxCompute source table reads MaxCompute partitioned table T with the partition key column of ds. Data in table T is written in the following methods:

    -   Not recommended: Create a partition, for example, ds=20191010 and then write data into it. If the incremental MaxCompute source table consumes table T and detects new partition ds=20191010, it reads data from the new partition immediately. If some data is not written into the partition, the data fails to be read.
    -   Recommended: Execute the Insert overwrite table T partition \(ds='20191010'\) ... statement without the need to create a partition. After the job succeeds, both the partition and data are displayed.

