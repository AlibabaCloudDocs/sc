---
keyword: [source table, MaxCompute]
---

# Create a full MaxCompute source table

This topic describes how to create a full MaxCompute source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and FAQ involved when you create a full MaxCompute source table.

**Note:** A MaxCompute connector can be used to store data of a source table for streaming jobs and batch jobs.

## Introduction to MaxCompute

MaxCompute is a fast and fully managed computing platform for large-scale data warehousing. MaxCompute can process exabytes of data. It provides solutions for storing and computing mass structured data in data warehouses and provides analytics and modeling services. For more information about MaxCompute, see [What is MaxCompute?](/intl.en-US/Product Introduction/What is MaxCompute?.md).

## DDL syntax

```
create table odps_source(
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

**Note:** The parameters in the WITH clause must be all lowercase.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `odps`.|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|No|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md). **Note:** This parameter is required if MaxCompute is deployed in a virtual private cloud \(VPC\). |
|project|The name of a MaxCompute project.|Yes|None.|
|tableName|The name of the MaxCompute table.|Yes|None.|
|accessId|AccessKey ID|Yes|None.|
|accessKey|AccessKey Secret|Yes|None.|
|partition|The name of a partition.|No|-   A MaxCompute table that has hash partitions

For example, if only one partition key column `ds` exists, ``partition` = 'ds=20180905'` indicates that data in the `ds=20180905` partition is read.

-   A MaxCompute table that has multi-level partitions

For example, if two partition key columns `ds` and `hh` exist, ``partition`='ds=20180905,hh=*'` indicates that the data in the `ds=20180905` partition is read.

**Note:** You must declare the values of all partitions when you filter partitions. In the preceding example, if you declare only `'partition' = 'ds=20180905'`, no partition data is read. |

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
|DECIMAL|DECIMAL|
|BINARY|VARBINARY|
|STRING|VARCHAR|

## Sample code

```
CREATE TEMPORARY TABLE odps_source (
   cid varchar,
   rt DOUBLE
) with (
   'connector' = 'odps', 
   'endpoint' = '<yourEndpointName>', 
   'tunnelEndpoint' = '<yourTunnelEndpoint>',
   'project' = '<yourProjectName>',
   'tablename' = '<yourTableName>',
   'accessid' = '<yourAccessId>',
   'accesskey' = '<yourAccessPassword>',
   'partition' = 'ds=20180905'
);

CREATE TEMPORARY TABLE blackhole_sink (
   cid varchar,
   invoke_count BIGINT
) with (
   'connector'='blackhole'
);

INSERT INTO blackhole_sink 
SELECT 
   cid,
   count(*) as invoke_count
FROM odps_source GROUP BY cid;
```

## FAQ

-   Q: What do the endPoint and tunnelEndpoint parameters mean? What happens if the two parameters are incorrectly configured?

    A: For more information about the endPoint and tunnelEndpoint parameters, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md). If the two parameters are incorrectly configured in a VPC, a task error may occur.

    -   If the endPoint parameter is incorrectly configured, the task stops at a progress of 91%.
    -   If the tunnelEndpoint parameter is incorrectly configured, the task fails.
-   Q: How does the full MaxCompute data storage read data from a MaxCompute source table?

    A: The full MaxCompute data storage reads data from the full MaxCompute source table by using a tunnel. Therefore, the read speed and bandwidth are restricted by the bandwidth of the tunnel used by the full MaxCompute source table.

-   Q: Can the data that is appended to the full MaxCompute source table or the partitions of the table be read after a Flink job is started?

    A: No, the new data cannot be read. The full MaxCompute data storage reads data from tables or partitions by using a tunnel. After a Flink job is started, the reader exits when data reading is complete. Then, the reader does not read new data from the full MaxCompute source table or the partitions of the table.

-   Q: How do I view the partition names of a full MaxCompute source table?

    A: To view the partition names of a full MaxCompute source table on table details page, you can perform the following steps:

    1.  Go to the [Data Map](https://meta.dw.alibaba-inc.com/store/index.html) page to search for the table name.
    2.  Click the table name.
    3.  On the right side of the table details page, click the **Details** tab and then the **Partitions** tab. You can view MaxCompute partition names in the **Partition Name** column.
-   Q: If a MaxCompute source table is referenced as a data source, can the data that is appended to the source table or partitions in the table be read after a job is started?

    A: No, the data cannot be read and the job may fail. The full MaxCompute data storage uses `ODPS DOWNLOAD SESSION` to read data from tables or partitions. When you create a `DOWNLOAD SESSION`, the MaxCompute server creates an index file, which contains the data mapping obtained when the `DOWNLOAD SESSION` is created. Subsequent data reading is performed based on the data mapping. Therefore, the data that is appended to the table or partitions in the table after `DOWNLOAD SESSION` is created cannot be read in normal cases. This issue occurs in the following scenarios:

    -   When the MaxCompute data storage reads data by using a tunnel, the following error is returned if new data is written to the table or partitions in the table: `ErrorCode=TableModified,ErrorMessage=The specified table has been modified since the download initiated.`
    -   New data is written to the table or partitions in the table. However, the tunnel through which data is read is disabled Therefore, the new data cannot be read. If a job is recovered from failure or is resumed, the data may be incorrect. For example, existing data is read again but the newly added data may not be read completely.
-   Q: Can I suspend and then resume a job for a full MaxCompute source table? Can I also change the parallelism of the full MaxCompute source table?

    A: MaxCompute determines which data in which partitions need to be read for each parallel job and records the consumption information for each parallel job in the state based on the parallelism. This way, MaxCompute can continue reading data from the last read position after the job is suspended and then resumed or fails.

    If you suspend and then resume a job for an incremental MaxCompute source table after you change the parallelism of the source table, the impact on the job cannot be estimated because some data may be read repeatedly but some data may not be read.

-   Q: Why are the partitions before the start offset also read when I set the start time to `2019-10-11 00:00:00` for a job?

    A: The start offset is valid only for message queue data sources such as DataHub. It is invalid for MaxCompute source tables. After you start a Flink job, Flink reads data in the following ways:

    -   For a partitioned table, it reads all current partitions.
    -   For a non-partitioned table, it reads all existing data.

