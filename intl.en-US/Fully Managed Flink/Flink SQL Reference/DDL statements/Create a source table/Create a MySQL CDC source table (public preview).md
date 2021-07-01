---
keyword: [CDC of MySQL, CDC]
---

# Create a MySQL CDC source table \(public preview\)

This topic describes how to create a MySQL Change Data Capture \(CDC\) source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a MySQL CDC source table.

**Note:**

-   MySQL CDC source tables are in public preview. If you have high requirements for job stability, we recommend that you do not use MySQL CDC source tables.
-   This topic applies only to Ververica Platform \(VVP\) 2.3.0 and later.
-   MySQL CDC data can be consumed only in a single processing job.
-   When MaxCompute reads full data from a MySQL CDC source table, checkpointing does not take effect. In this case, we recommend that you do not enable Autopilot in this scenario.
-   MySQL CDC source tables do not support watermarks. If you need to perform window aggregation on a MySQL CDC source table, you can use a non-window aggregation method to aggregate data instead. For more information, see [How do I perform window aggregation if watermarks are not supported?](#section_y6t_st6_f0a)

## Introduction to MySQL CDC source tables

A MySQL CDC source table is a streaming source table of MySQL. This table is used to read full and incremental data from a MySQL database. The exactly-once processing semantics is used to ensure data reading accuracy. Before Flink starts a full table scan, it adds a global read lock by using FLUSH TABLES WITH READ LOCK, obtains the binary log file position and table schemas, and then releases the global read lock. After Flink reads all data in a full table scan, it obtains the incremental change records from the previous binary log file position. When a Flink job is running, checkpoints are periodically generated to record the binary log file position. If the job is recovered from failure or is resumed, Flink can process data from the previous binary log file position. This achieves the exactly-once semantics.

**Note:** Only MySQL users who have specific permissions such as SELECT, RELOAD, SHOW DATABASES, REPLICATION SLAVE, and REPLICATION CLIENT are allowed to read full data and incremental data from a MySQL CDC source table. The MySQL CDC source table connector can read data from MySQL V5.7 and MySQL V8.0.

## Usage notes

-   We recommend that you grant MySQL users the RELOAD permission.

    If you do not grant MySQL users the RELOAD permission, the global read lock is downgraded to a table read lock. The table read lock is released only after a full table scan is complete. This prolongs the locking duration. In addition, online business is affected because the table read lock blocks data writing to tables.

    **Note:** If you have configured 'debezium.snapshot.locking.mode' = 'none' to show the stage of skipping the read lock, you do not need to grant the RELOAD permission.

-   Online business may be affected when you execute FLUSH TABLES WITH READ LOCK.

    When you execute FLUSH TABLES WITH READ LOCK, Flink obtains the binary log file position and table schemas. Therefore, the locking duration increases with the number of tables. The database locking duration may reach seconds. For example, the duration for locking thousands of tables is about 2 or 3 seconds. Online business may be affected because the read lock blocks data writing. If you want to skip the lock stage and can tolerate non-exactly-once semantics, you can add the 'debezium.snapshot.locking.mode' = 'none' property to explicitly skip the lock stage.

-   Unique server IDs must be explicitly configured for each job.

    Each client that synchronizes data from a database has a unique server ID. The MySQL server maintains network connections and binary log file positions based on server IDs. If a large number of clients with different server IDs connect to the MySQL server, CPU utilization of the MySQL server may increase suddenly. This affects the stability of online business. If multiple jobs share the same server ID, the checkpoints for the binary log file positions may be disordered and data reading accuracy is affected. Therefore, we recommend that you use dynamic hints to configure different server IDs for each CDC job, such as `SELECT * FROM source_table /*+ OPTIONS('server-id'='123456') */ ;`. For more information about dynamic hints, see [Dynamic hints](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/sql/hints.html).

-   No checkpoint can be generated during a full table scan.

    During a full table scan, Flink cannot trigger a checkpoint because no binary log file position can be used to restore data. To prevent Flink from triggering a checkpoint, the MySQL CDC source table keeps the running checkpoint waiting. If the table that is scanned is excessively large, the scan is time-consuming, which causes the checkpoint to time out. The checkpoint that times out is considered a failed checkpoint. If Flink uses its default configurations, a job failover may be triggered. To prevent job failures due to the timeout of a checkpoint, we recommend that you configure the following job parameters for excessively large tables:

    ```
    execution.checkpointing.interval: 10min
    execution.checkpointing.tolerable-failed-checkpoints: 100
    restart-strategy: fixed-delay 
    restart-strategy.fixed-delay.attempts: 2147483647
    ```

    |Parameter|Description|
    |---------|-----------|
    |execution.checkpointing.interval|The checkpoint interval. Unit: minutes.|
    |execution.checkpointing.tolerable-failed-checkpoints|The total number of tolerable checkpoint failures.|
    |restart-strategy|The retry policy after a checkpoint failure. Valid values:    -   fixed-delay: This is the recommended value. If an application fails, the system restarts the application at a fixed interval. The default interval is 1 second. The system can restart the application for a maximum number of times that is specified by restart-strategy.fixed-delay.attempts.
    -   failure-rate: If an application fails, the system restarts the application at a fixed interval. The default interval is 1 second. If the failure frequency exceeds the specified upper limit, the application is no longer restarted. The failure frequency is controlled by the following parameters:
        -   restart-strategy.failure-rate.failure-rate-interval: determines the failure interval.
        -   restart-strategy.failure-rate.max-failures-per-interval: determines the maximum number of failures in the specified interval.
    -   none: If an application fails, the system does not restart the application. |
    |restart-strategy.fixed-delay.attempts|The number of retries for checkpoint failures.|


## DDL syntax

```
CREATE TABLE mysqlcdc_source (
  order_id INT,
  order_date TIMESTAMP(0),
  customer_name STRING,
  price DECIMAL(10, 5),
  product_id INT,
  order_status BOOLEAN
) WITH (
  'connector' = 'mysql-cdc',
  'hostname' = '<yourHostname>',
  'port' = '3306',
  'username' = '<yourUsername>',
  'password' = '<yourPassword>',
  'database-name' = '<yourDatabaseName>',
  'table-name' = '<yourTableName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the source table.|Yes|STRING|Set the value to `mysql-cdc`.|
|hostname|The IP address or hostname of the MySQL database.|Yes|STRING|None.|
|username|The username that is used to access the MySQL database.|Yes|STRING|None.|
|password|The password that is used to access the MySQL database.|Yes|STRING|None.|
|database-name|The name of the MySQL database.|Yes|STRING|The database name can be a regular expression so that data of multiple databases can be read.|
|table-name|The name of the MySQL table.|Yes|STRING|The table name can be a regular expression so that data of multiple tables can be read.|
|port|The port that is used to access the MySQL database.|No|INTEGER|Default value: 3306.|
|server-id|The ID allocated to a database client.|No|STRING|The ID must be unique in a MySQL cluster. We recommend that you configure a unique server ID for each job in the same database. By default, a value in the range of 5400 to 6400 is randomly generated.|
|scan.startup.mode|The startup mode of the consumer.|No|STRING|For more information, see [Start mode](#section_l9e_7i8_wa4).|
|scan.startup.timestamp-millis|The timestamp of the start offset when the scan.startup.mode parameter is set to timestamp.|No|LONG|Unit: milliseconds.|
|scan.startup.specific-offset.file|The name of the binary log file that Flink needs to read when the scan.startup.mode parameter is set to specific-offset.|No|STRING|This parameter must be used with the scan.startup.specific-offset.pos parameter.|
|scan.startup.specific-offset.pos|The offset of the binary log file that Flink needs to read when the scan.startup.mode parameter is set to specific-offset.|No|INTEGER|This parameter must be used with the scan.startup.specific-offset.file parameter.|
|server-time-zone|The time zone of the session used by the database.|No|STRING|Example: Asia/Shanghai. This parameter determines how to convert data in the MySQL database from the TIMESTAMP type to the STRING type.|
|debezium.min.row.count.to.stream.results|The minimum number of data records allowed in a table to trigger the batch data read mode. If the number of data records in a table is greater than the value of this parameter, the batch data read mode is used.|No|INTEGER|Default value: 1000. Flink reads data from a MySQL source table in one of the following modes:-   Full data read: reads all data from the table and writes the data to the memory. The read speed is high, but some memory space is consumed. If the source table contains a large amount of data, out of memory \(OOM\) may occur in this mode.
-   Batch data read: reads data of a specific number of rows at a time until all data is read. OOM is prevented even if the table contains a large amount of data. However, the read speed is low. |
|debezium.snapshot.fetch.size|The maximum number of rows that can be read at a time from a MySQL source table at the snapshot stage.|No|INTEGER|This parameter is valid only when the batch read mode is used.|
|debezium.\*|The Debezium property parameters.|No|STRING|This parameter is used to impose fined-grained control over the behavior of a Debezium connector. For example, you can set `debezium.snapshot.mode` to never to ensure that the connector never reads snapshots. For more information, see [Configure properties](https://debezium.io/documentation/reference/1.2/connectors/mysql.html#mysql-connector-configuration-properties_debezium).|

## Start mode

The scan.startup.mode parameter specifies the startup mode of MySQL CDC consumers. Valid values:

-   initial: This is the default value. When the MySQL CDC source table connector starts for the first time, it scans all historical data and reads the latest binary log data.
-   earliest-offset: When the MySQL CDC source table connector starts for the first time, it reads binary log data from the earlier offset, instead of scanning all historical data.
-   latest-offset: When the MySQL CDC source table connector starts for the first time, it reads binary log data from the latest offset, instead of scanning all historical data. This way, Flink reads only the latest incremental data after the connector starts.
-   timestamp: When the MySQL CDC source table connector starts for the first time, it reads binary log data from the offset specified by the timestamp, instead of scanning all historical data. The MySQL CDC source table connector traverses the data in the binary log file, specifies that the position of the timestamp is earlier than the time of the change event, and then reads binary log data from this position.

    If the scan.startup.mode parameter is set to timestamp, you also need to configure the scan.startup.timestamp-millis parameter to specify a start timestamp in milliseconds. The value of this parameter indicates the number of milliseconds from 1970-01-01 00:00:00 UTC to the current time.

-   specific-offset: When the MySQL CDC source table connector starts for the first time, it reads data from the specified binary log file and the offset specified in the binary log file, instead of scanning all historical data.

    If the scan.startup.mode parameter is set to specific-offset, you also need to configure the scan.startup.specific-offset.file and scan.startup.specific-offset.pos parameters. scan.startup.specific-offset.file indicates the name of a binary log file. scan.startup.specific-offset.pos indicates the offset in a binary log file.

    Example:

    ```
    'scan.startup.specific-offset.file' = 'mysql-bin.000021'
    'scan.startup.specific-offset.pos' = '30292'
    ```

    You can use MySQL SQL commands to query the name of a binary log file and the offset in the binary log file.

    -   To query the name of a binary log file, run the following command:

        ```
        show binary logs
        ```

    -   To query the offset in the binary log file, run the following command:

        ```
        show binlog events in 'mysql-bin.xxxx'
        ```


**Note:** The scan.startup.mode parameter uses the snapshot.mode parameter of Debezium at the underlying layer. If you use scan.startup.mode and debezium.snapshot.mode at the same time in data definition language \(DDL\) statements, scan.startup.mode may not take effect.

If the debezium.snapshot.mode parameter is used alone, the debezium.snapshot.mode parameter still takes effect.

## Field type mapping

The following table lists the data type mapping between MySQL CDC and Flink.

|Data type of MySQL CDC|Data type of Flink|
|----------------------|------------------|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|TINYINT UNSIGNED|
|INT|INT|
|MEDIUMINT|
|SMALLINT UNSIGNED|
|BIGINT|BIGINT|
|INT UNSIGNED|
|BIGINT UNSIGNED|DECIMAL\(20, 0\)|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|DOUBLE PRECISION|
|NUMERIC\(p, s\)|DECIMAL\(p, s\)|
|DECIMAL\(p, s\)|
|BOOLEAN|BOOLEAN|
|TINYINT\(1\)|
|DATE|DATE|
|TIME \[\(p\)\]|TIME \[\(p\)\] \[WITHOUT TIMEZONE\]|
|DATETIME \[\(p\)\]|TIMESTAMP \[\(p\)\] \[WITHOUT TIMEZONE\]|
|TIMESTAMP \[\(p\)\]|TIMESTAMP \[\(p\)\]|
|TIMESTAMP \[\(p\)\] WITH LOCAL TIME ZONE|
|CHAR\(n\)|STRING|
|VARCHAR\(n\)|
|TEXT|
|BINARY|BYTES|
|VARBINARY|
|BLOB|

## FAQ

-   Q: How do I perform window aggregation if watermarks are not supported?

    A: If you need to perform window aggregation on a MySQL CDC source table, you can convert time fields into window values and use GROUP BY to aggregate the window values. For example, if you want to calculate the number of orders and sales volume per minute in each store, you can use `SELECT shop_id, DATE_FORMAT(order_ts, 'yyyy-MM-dd HH:mm'), COUNT(*), SUM(price) FROM order_mysql_cdc GROUP BY shop_id, DATE_FORMAT(order_ts, 'yyyy-MM-dd HH:mm')`.

-   Q: How do I enable the system to skip the snapshot stage and read only the incremental data?

    A: You can use the debezium.snapshot.mode parameter in the WITH clause to determine whether to skip the snapshot stage. Valid values:

    -   never: When a job starts to run, the system directly reads the incremental data from the start position without the need to read the snapshots from the database. The old incremental data of the MySQL database may be automatically deleted. Therefore, the incremental data may not contain full data and the data that the system reads may be incomplete.
    -   schema\_only: If you do not need to ensure data consistency and focus only on the incremental data in the database, you can set this parameter to schema\_only. This way, only schema snapshots are generated and the system reads data from the latest incremental data.
-   Q: How does the system read data from a MySQL database on which database and table sharding is performed?

    A: If a MySQL database has multiple tables such as table user\_00, table user\_02, and table user\_99 after database and table sharding, and the schemas of these tables are the same, you can use the table-name option to specify a regular expression to match multiple tables whose data can be read. For example, you can set table-name to user\_.\* to monitor all tables with the user\_ prefix. You can also use the database-name option to achieve the same effect if the schemas of all tables in the database are the same.

-   Q: What do I do if the data reading efficiency is low and backpressure occurs when full data is read from tables?

    A: The issue may be caused because the downstream node processes data at a low speed. You can first check whether the downstream node has backpressure. If the downstream node has backpressure, solve the backpressure issue on the downstream node first. To resolve this issue, you can use one of the following methods:

    -   Increase the parallelism.
    -   Enable aggregate optimization features, such as miniBatch.

