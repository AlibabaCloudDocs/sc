---
keyword: [MySQL的CDC, CDC]
---

# MySQL的CDC源表（公测中）

本文为您介绍MySQL的CDC（Change Data Capture）源表DDL定义、WITH参数、类型映射和代码示例。

**说明：**

-   MySQL的CDC源表正处于公测中，如果您对作业稳定性要求较高时，建议不要使用MySQL的CDC源表。
-   本文仅适用于VVP 2.3.0及以上版本。
-   仅支持单并发消费CDC-MySQL数据。
-   在全量读取MYSQL CDC源表时，Checkpoint不生效，因此读取全量MYSQL CDC源表时，不建议开启自动调优。
-   MySQL CDC 源表目前不支持定义Watermark。如果您需要进行窗口聚合，您可以采用非窗口聚合的方式，详情请参见[不支持定义Watermark，那如何进行窗口聚合？](#section_y6t_st6_f0a)

## 什么是MySQL的CDC源表

MySQL的CDC源表，即MySQL的流式源表，支持对MySQL数据库的全量和增量读取，并保证Exactly Once，不多读一条也不少读一条数据。其工作机制是，在启动扫描全表前，先加一个全局读锁（FLUSH TABLES WITH READ LOCK），然后获取此时的Binlog位点以及表的schema，紧接着释放全局读锁。随后开始扫描全表，当全表数据读取完后，会从之前获取的Binlog位点获取增量的变更记录。在Flink作业运行期间会周期性执行checkpoint，记录下Binlog位点，当作业发生Failover，便会从之前记录的Binlog位点继续处理，从而实现Exactly Once语义。

**说明：** MySQL的CDC源表需要一个有特定权限（包括SELECT、RELOAD、SHOW DATABASES、REPLICATION SLAVE和REPLICATION CLIENT）的MySQL用户，才能读取全量和增量数据。MySQL CDC Connector支持读取的MySQL版本为5.7和8.0。

## 注意事项

-   建议对MySQL用户授予RELOAD权限

    如果您未对MySQL用户授予RELOAD权限，则全局读锁会降级为表级读锁，而使用表级读锁需要等到全表扫描完成，才能释放锁，所以持锁时间会较长，而读锁会阻塞往表内写入数据，影响线上业务。

    **说明：** 如果您已经配置了'debezium.snapshot.locking.mode' = 'none'属性来显示跳过读锁阶段，则不用授予RELOAD权限。

-   全局读锁（FLUSH TABLES WITH READ LOCK）的影响

    全局读锁阶段会去获取Binlog位点以及表的Schema，因此其持锁耗时与表的数量成正比，数据库持锁耗时可能达到秒级，例如上千张表大概两三秒。而读锁是会阻塞写入操作，因此仍可能对线上业务造成影响。如果您希望跳过锁阶段，且能容忍非Exactly Once语义，则可以通过增加 'debezium.snapshot.locking.mode' = 'none'属性来显式跳过锁阶段。

-   每个作业需显式配置不同的SERVER ID

    每个同步数据库数据的客户端，都会有一个唯一ID，即SERVER ID。MySQL SERVER会根据该ID来维护网络连接以及Binlog位点。因此如果有大量不同的SERVER ID的客户端一起连接MySQL SERVER，可能导致MySQL SERVER的CPU陡增，影响线上业务稳定性。此外，多个作业共享相同的SERVER ID，会导致Binlog位点错乱，多读或少读数据。因此建议您通过动态Hints，在每个CDC作业都配置上不同的SERVER ID，例如`SELECT * FROM source_table /*+ OPTIONS('server-id'='123456') */ ;`。动态Hints详情请参见[动态Hints](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/sql/hints.html)。

-   全表扫描阶段无法执行checkpoint

    在扫描全表数据时，没有可用于恢复的位点，所以无法在全表扫描阶段去执行checkpoint。为了不执行checkpoint，MySQL的CDC源表会让执行中的checkpoint一直等待，甚至checkpoint超时（如果表超级大，扫描耗时非常长）。超时的checkpoint会被认为是failed checkpoint，Flink默认配置下，会触发Flink的Failover。因此建议当表超大时，为了避免因为checkpoint超时而导致作业失败，可以配置如下作业参数：

    ```
    execution.checkpointing.interval: 10min
    execution.checkpointing.tolerable-failed-checkpoints: 100
    restart-strategy: fixed-delay 
    restart-strategy.fixed-delay.attempts: 2147483647
    ```

    |参数|说明|
    |--|--|
    |execution.checkpointing.interval|checkpoint间隔时间，单位为分钟。|
    |execution.checkpointing.tolerable-failed-checkpoints|checkpoint失败容忍重试的总次数。|
    |restart-strategy|checkpoint失败后的重试策略，取值如下：    -   fixed-delay（推荐值）：遇到失败，以固定间隔（默认1秒）重启应用，并最多重启restart-strategy.fixed-delay.attempts次。
    -   failure-rate：失败后会以固定间隔（默认1s）重启应用，失败频率如果超过指定值，则不再重启。失败频率通过两个参数控制
        -   restart-strategy.failure-rate.failure-rate-interval：确定衡量的时间间隔。
        -   restart-strategy.failure-rate.max-failures-per-interval：确定时间间隔中最多失败的次数。
    -   none：失败后不重启。 |
    |restart-strategy.fixed-delay.attempts|checkpoint失败后的重试次数。|


## DDL定义

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

## WITH参数

|参数|说明|是否必填|数据类型|备注|
|--|--|----|----|--|
|connector|源表类型。|是|STRING|固定值为`mysql-cdc`。|
|hostname|MySQL数据库的IP地址或者Hostname。|是|STRING|无。|
|username|MySQL数据库服务的用户名。|是|STRING|无。|
|password|MySQL数据库服务的密码。|是|STRING|无。|
|database-name|MySQL数据库名称。|是|STRING|数据库名称支持正则表达式以读取多个数据库的数据。|
|table-name|MySQL表名。|是|STRING|表名支持正则表达式以读取多个表的数据。|
|port|MySQL数据库服务的端口号。|否|INTEGER|默认值为3306。|
|server-id|数据库客户端的一个ID。|否|STRING|该ID必须是MySQL集群中全局唯一的。建议针对同一个数据库的每个作业都设置一个不同的ID。默认会随机生成一个5400~6400的值。|
|scan.startup.mode|消费者的启动模式。|否|STRING|详情请参见[启动模式](#section_l9e_7i8_wa4)。|
|scan.startup.timestamp-millis|在timestamp启动模式下，指定启动位点时间戳。|否|LONG|单位为毫秒。|
|scan.startup.specific-offset.file|在specific-offset启动模式下，指定读取的 binlog文件名。|否|STRING|需要与scan.startup.specific-offset.pos参数一起使用。|
|scan.startup.specific-offset.pos|在specific-offset启动模式下，指定读取的binlog文件中的位点。|否|INTEGER|需要与scan.startup.specific-offset.file参数一起使用。|
|server-time-zone|数据库在使用的会话时区。|否|STRING|例如Asia/Shanghai，该参数控制了MySQL中的TIMESTAMP类型如何转成STRING类型。|
|debezium.min.row.count.to.stream.results|当表的条数大于该值时，会使用分批读取模式。|否|INTEGER|默认值为1000。Flink采用以下方式读取MySQL源表数据：-   全量读取：直接将整个表的数据读取到内存里。优点是速度快，缺点是会消耗对应大小的内存，如果源表数据量非常大，可能会有OOM风险。
-   分批读取：分多次读取，每次读取一定数量的行数，直到读取完所有数据。优点是读取数据量比较大的表没有OOM风险，缺点是读取速度相对较慢。 |
|debezium.snapshot.fetch.size|在Snapshot阶段，每次读取MySQL源表数据行数的最大值。|否|INTEGER|仅当分批读取模式时，该参数生效。|
|debezium.\*|Debezium属性参数。|否|STRING|从更细粒度控制Debezium客户端的行为。例如`'debezium.snapshot.mode' = 'never'`，详情请参见[配置属性](https://debezium.io/documentation/reference/1.2/connectors/mysql.html#mysql-connector-configuration-properties_debezium)。|

## 启动模式

配置参数scan.startup.mode指定了MySQL CDC消费者的启动模式，可选参数包括：

-   initial \(默认\)：在第一次启动时，会先扫描历史全量数据，然后读取最新的binlog数据。
-   earliest-offset：在第一次启动时，不会扫描历史全量数据，直接从binlog的起点开始读取。
-   latest-offset：在第一次启动时，不会扫描历史全量数据，直接从binlog的末尾（最新的binlog处）开始读取，即只读取该Connector启动以后的最新变更。
-   timestamp：在第一次启动时，不会扫描历史全量数据，直接读取指定timestamp位点以后的binlog数据。CDC Source会遍历binlog文件中的数据，指定时间戳的位置要早于变更事件的时间，从该位置开始读取binlog数据。

    如果您指定的启动模式为timestamp，则还需要配置scan.startup.timestamp-millis参数，指定一个启动时间戳的毫秒值，该值代表从1970-01-01 00:00:00 UTC到当前的毫秒数。

-   specific-offset：在第一次启动时，不会扫描历史全量数据，直接从指定的binlog文件和binlog文件中指定的位点开始读取binlog数据。

    如果您指定的启动模式为specific-offset，则还需配置scan.startup.specific-offset.file（binlog文件名）和scan.startup.specific-offset.pos（binlog文件内的位点）两个参数。

    配置信息示例如下：

    ```
    'scan.startup.specific-offset.file' = 'mysql-bin.000021'
    'scan.startup.specific-offset.pos' = '30292'
    ```

    您可以使用MySQL SQL命令查询binlog文件名和binlog文件内的位点，具体命令如下：

    -   查询binlog文件名

        ```
        show binary logs
        ```

    -   查询binlog文件内的位点

        ```
        show binlog events in 'mysql-bin.xxxx'
        ```


**说明：** scan.startup.mode底层利用了Debezium的snapshot.mode参数，因此请不要在DDL中混合使用scan.startup.mode和debezium.snapshot.mode两个参数，否则scan.startup.mode可能不会生效。

单独使用debezium.snapshot.mode参数，debezium.snapshot.mode参数仍然生效，scan.startup.mode和debezium.snapshot.mode两个参数一起使用，则只有scan.startup.mode生效。

## 类型映射

MySQL的CDC和Flink字段类型对应关系如下。

|MySQL CDC字段类型|Flink字段类型|
|-------------|---------|
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

## 常见问题

-   问题：不支持定义Watermark，那如何进行窗口聚合？

    解决方案：如果您需要在MySQL CDC源表上进行窗口聚合，可以考虑采用非窗口聚合的方式，即将时间字段转换成窗口值，然后根据窗口值进行GROUP BY聚合。例如，统计每个店铺每分钟的订单数和销量，代码为`SELECT shop_id, DATE_FORMAT(order_ts, 'yyyy-MM-dd HH:mm'), COUNT(*), SUM(price) FROM order_mysql_cdc GROUP BY shop_id, DATE_FORMAT(order_ts, 'yyyy-MM-dd HH:mm')`。

-   问题：如何跳过Snapshot阶段，只从变更数据开始读取？

    解决方案：可以通过WITH参数debezium.snapshot.mode来控制，您可以设置为：

    -   never：在启动时，不读取数据库的快照，而是直接从变更的最开始位置读取。但需要注意MySQL的变更旧数据可能会被自动清理，因此不能保证变更数据中包含了全量的数据，读取的数据不完整。
    -   schema\_only：如果你不需要保证数据的一致性，只关心作业启动后数据库的新增变更，则可以设置为schema\_only，仅快照schema，不快照数据，从变更的最新数据开始读取。
-   问题：如何读取一个分库分表的MySQL数据库？

    解决方案：如果MySQL是一个分库分表的数据库，分成了user\_00、user\_02和user\_99等多个表，且所有表的schema一致。则可以通过table-name选项，指定一个正则表达式来匹配读取多张表，例如设置table-name为user\_.\*，监控所有user\_前缀的表。database-name选项也支持该功能，但需要所有的表schema一致。

-   问题：全表读取阶段效率慢、存在反压，应该如何解决？

    解决方案：可能是下游节点处理太慢导致反压了。因此您需要先排查下游节点是否存在反压。如果存在，则需要先解决下游节点的反压问题。您可以通过以下方式处理：

    -   增加并发度。
    -   开启minibatch等聚合优化参数（下游聚合节点）。

