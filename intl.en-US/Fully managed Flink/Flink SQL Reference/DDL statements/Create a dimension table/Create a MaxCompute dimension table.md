---
keyword: [dimension table, MaxCompute]
---

# Create a MaxCompute dimension table

This topic describes how to create a MaxCompute dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, field type mappings, and FAQ involved when you create a MaxCompute dimension table.

## DDL syntax

```
create table odps_dim(
    id VARCHAR,
    name VARCHAR,
    age int,
    PRIMARY KEY (id, name) not enforced
) with (
    'connector' = 'odps', 
    'endpoint' = '<yourEndpoint>',
    'tunnelEndpoint' = '<yourTunnelEndpoint>',
    'project' = '<yourProjectName>',
    'tablename' = '<yourTableName>',
    'accessid' = '<yourAccessKeyId>',
    'accesskey' = '<yourAccessKeySecret>',
    'partition' = 'ds=2018****',
    'cache' = 'ALL'
);
```

**Note:**

-   When you declare a dimension table, you must specify a primary key. The primary key of a MaxCompute dimension table must be unique. Duplicate primary keys are deleted.
-   When you join a dimension table with another table, the ON condition must contain equivalence conditions that include all primary keys.
-   You are not allowed to write the partition key columns of a MaxCompute dimension table to the DDL statement.
-   The primary key fields must be placed before non-primary key fields and in the sequence of the fields in the parentheses of PRIMARY KEY.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `odps`.|
|endPoint|The endpoint of MaxCompute.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|tunnelEndpoint|The endpoint of the MaxCompute Tunnel service.|Yes|For more information, see [Regions where MaxCompute is available and corresponding endpoints](/intl.en-US/Prepare/Configure endpoints.md).|
|project|The name of a MaxCompute project.|Yes|None.|
|tableName|The name of the table.|Yes|None.|
|accessId|The AccessKey ID that is used to access the database.|Yes|None.|
|accessKey|The AccessKey secret that is used to access the database.|Yes|None.|
|partition|The name of a partition.|No|-   Fixed partitions
    -   A MaxCompute source table that has only one partition

For example, if only one partition key column `ds` exists, ``partition` = 'ds=20180905'` indicates that the data in the `ds=20180905` partition is read.

    -   A MaxCompute source table that has multiple partitions

For example, if two partition key columns `ds` and `hh` exist, ``partition`='ds=20180905,hh=*'` indicates that the data in the `ds=20180905` partition is read.

**Note:** You must declare the values of all partitions when you filter partitions. In the preceding example, if you declare only ``partition` = 'ds=20180905'`, no partition is read.

-   Dynamic partitions
    -   `'partition' = 'max_pt()'` is supported. It indicates that the partition that ranks first in alphabetical order among all partitions is loaded.
    -   ``partition` = 'max_pt_with_done()'` is supported. It indicates that the partition that ranks first in alphabetical order among all partitions and ends with the `.done` suffix is loaded. |

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|You must set the cache parameter to `ALL` for a MaxCompute dimension table and explicitly declare this setting in the DDL statement. ALL: indicates that all data in the dimension table is cached. Before a job starts to run, the system loads all the data in the dimension table to the cache. If you need to retrieve data from the dimension table, the system searches the cache for all subsequent queries in the dimension table. If the system does not find the data record in the cache, the join key does not exist. The system loads all data in the cache again after cache entries expire.

If the data amount of a remote table is small and a large number of missing keys exist, we recommend that you set this parameter to ALL. \(The source table and dimension table cannot be associated based on the ON clause.\) If you set this parameter to ALL, you must specify the cacheTTLMs and cacheReloadTimeBlackList parameters.

**Note:**

-   If you set the cache parameter to ALL, you must increase the memory of the node where the dimension table is joined with another table. This is because the system asynchronously loads data from the dimension table. The increased memory size is at least four times the size of the data in the remote table. The actual increased memory size varies based on the algorithm that is used to compress the stored data in MaxCompute.
-   If you use an ultra-large MaxCompute dimension table, frequent garbage collections that are triggered by allocation failures may cause job exceptions. To resolve this issue, you can increase the memory of the node where the dimension table is joined with another table. If the issue persists, we recommend that you convert the dimension table to a key-value dimension table that supports the least recently used \(LRU\) cache policy. For example, you can use an ApsaraDB for HBase dimension table as the key-value dimension table. |
|cacheSize|The maximum number of data records that can be cached.|You can specify the cacheSize parameter based on your business requirements. By default, data of 100,000 rows in a MaxCompute dimension table can be cached.|
|cacheTTLMs|The cache timeout period.|Unit: milliseconds. If the cache parameter is set to `ALL`, the cacheTTLMs parameter specifies the interval at which the cache is refreshed. The cache is not refreshed by default.|
|cacheReloadTimeBlackList|The time periods during which the cache is not refreshed. This parameter takes effect when the cache parameter is set to ALL. The cache is not refreshed during the time periods that you specify for this parameter. This parameter is useful for massive online promotional events such as Double 11.|This parameter is empty by default. The following example shows the format of the values: `2017-10-24 14:00 -> 2017-10-24 15:00, 2017-11-10 23:30 -> 2017-11-11 08:00`. The following list describes the delimiters that you must use for the parameter values: -   Separate periods with commas \(`,`\).
-   Separate the start time and end time of each time period with a hyphen and a greater-than sign \(`->`\). |

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

## FAQ

-   Q: What is the difference between `max_pt()` and `max_pt_with_done()`?

    A: If the values of the partition parameter are sorted in alphabetical order, `max_pt()` returns the partition that ranks first in alphabetical order. If the values of the partition parameter are sorted in alphabetical order, `max_pt_with_done()` returns the partition that ranks first in alphabetical order and ends with the `.done` suffix.

    |Partition|
    |---------|
    |ds=20190101|
    |ds=20190101.done|
    |ds=20190102|
    |ds=20190102.done|
    |ds=20190103|

    Difference between `max_pt()` and `max_pt_with_done()`:

    -   ``partition`='max_pt_with_done()'` returns the `ds=20190102` partition.
    -   ``partition`='max_pt()'` returns the `ds=20190103` partition.
-   Q: How do I view the partition names of a MaxCompute table?

    A: Perform the following steps to view the partition names of a MaxCompute table:

    1.  Go to the [Data Map](https://meta.dw.alibaba-inc.com/store/index.html) page to search for the table name.
    2.  Click the table name.
    3.  On the right side of the table details page, click the **Details** tab and then the **Partitions** tab. You can view MaxCompute partition names in the **Partition Name** column.
-   Q: What do the endPoint and tunnelEndpoint parameters mean? What happens if the parameters are incorrectly configured?

    A: For more information about the endPoint and tunnelEndpoint parameters, see [Configure endpoints](/intl.en-US/Prepare/Configure endpoints.md). If the two parameters are incorrectly configured in a VPC, a task error may occur.

    -   If the endPoint parameter is incorrectly configured, the task stops at a progress of 91%.
    -   If the tunnelEndpoint parameter is incorrectly configured, the task fails.

