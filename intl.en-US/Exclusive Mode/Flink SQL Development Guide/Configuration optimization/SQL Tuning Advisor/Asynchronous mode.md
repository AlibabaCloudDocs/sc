---
keyword: [asynchronous mode, throughput, CPU utilization]
---

# Asynchronous mode

You can enable the asynchronous mode and configure related parameters to improve throughput when you join dimension tables.

## Background information

By default, the synchronous mode is used when you join dimension tables. The system queries the physical table and returns the association result each time a data record is added to the physical table. This results in low throughput and high latency. The asynchronous mode is introduced to process query requests in parallel, so that consecutive requests do not need to wait for processing.

Flink SQL implements asynchronous JOIN operations for dimension tables based on Flink Async I/O and asynchronous clients. This significantly improves throughput. The following figure shows the comparison between the synchronous and asynchronous modes.

![Comparison between synchronous and asynchronous modes](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9366343061/p161580.png)

## Optimization method

Add async='true' to the WITH clause in the data definition language \(DDL\) statement of a dimension table. The following table describes the parameters related to the asynchronous mode.

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|async|Specifies whether to enable the asynchronous mode.|No|Default value: false.|
|asyncResultOrder|Specifies whether to sort asynchronous results.|No|Valid values:-   unordered \(default value\)
-   ordered |
|asyncTimeoutMs|The timeout period of an asynchronous request, in milliseconds.|No|Default value: 180000.|
|asyncCapacity|The maximum number of asynchronous requests in an asynchronous request queue.|No|Default value: 100.|
|asyncCallbackThreads|The number of callback threads.|No|An asynchronous request is implemented by a thread. onComplete is called if an asynchronous request succeeds and onError is called if it fails. The number of times onComplete and onError is called determines the size of the thread pool. Default value: 50.|
|asyncConnectionQueueMaxsize|The maximum number of requests that can be sent.|No|If the number of requests waiting for a server to process reaches the value specified by this parameter, asynchronous request calling is blocked to prevent out of memory of the client. Default value: 100.|
|asyncCallbackQueueMaxsize|The maximum number of requests in a callback processing queue.|No|If the number of requests waiting for callback processing reaches the value specified by this parameter, asynchronous request calling is blocked to prevent out of memory of the client. Default value: 500.|

## Sample code

```
CREATE TABLE dim_cn_item(
    rowkey VARCHAR,
    item_id VARCHAR,
    title VARCHAR,
    cate_id VARCHAR,
    cate_name VARCHAR,
    cate_level1_id VARCHAR,
    cate_level2_id VARCHAR,
    cate_level3_id VARCHAR,
    cate_level1_name VARCHAR,
    cate_level2_name VARCHAR,
    cate_level3_name VARCHAR,
    pinlei_id VARCHAR,
    pinlei_name VARCHAR,
    bu_id VARCHAR,
    bu_name VARCHAR,
    PRIMARY KEY(rowkey)
) WITH(
    type='alihbase',
    diamondKey = 'xxxx',
    diamondGroup ='yyyy',
    cacheTTLMs='3600000',
    async='true',
    cache='LRU',
    columnFamily='cf',
    cacheSize='1000',
    tableName='yourTableName'
);
```

## FAQ

-   Error information

    ```
    Caused by: org.apache.flink.table.api.TableException: Output mode can not be UNORDERED if the input is an update stream.
    at org.apache.flink.table.plan.util.TemporalJoinUtil$.validate(TemporalJoinUtil.scala:340)
    at org.apache.flink.table.plan.nodes.common.CommonTemporalTableJoin.translateToPlanInternal(CommonTemporalTableJoin.scala:144)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlanInternal(StreamExecTemporalTableJoin.scala:98)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlanInternal(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.exec.ExecNode$class.translateToPlan(ExecNode.scala:58)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.org$apache$flink$table$plan$nodes$exec$StreamExecNode$$super$translateToPlan(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.exec.StreamExecNode$class.translateToPlan(StreamExecNode.scala:38)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlan(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecTemporalTableJoin.translateToPlan(StreamExecTemporalTableJoin.scala:39)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.translateToPlanInternal(StreamExecCalc.scala:89)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.translateToPlanInternal(StreamExecCalc.scala:43)
    at org.apache.flink.table.plan.nodes.exec.ExecNode$class.translateToPlan(ExecNode.scala:58)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.org$apache$flink$table$plan$nodes$exec$StreamExecNode$$super$translateToPlan(StreamExecCalc.scala:43)
    at org.apache.flink.table.plan.nodes.exec.StreamExecNode$class.translateToPlan(StreamExecNode.scala:38)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecCalc.translateToPlan(StreamExecCalc.scala:43)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecSink.translate(StreamExecSink.scala:158)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecSink.translateToPlanInternal(StreamExecSink.scala:103)
    at org.apache.flink.table.plan.nodes.physical.stream.StreamExecSink.translateToPlanInternal(StreamExecSink.scala:53)
    at org.apache.flink.table.plan.nodes.exec.ExecNode$class.translateToPlan(ExecNode.scala:58)
    at
    ```

-   Cause

    If the upstream data is Update Stream, the asyncResultOrder parameter is set to unordered when you join dimension tables.

    **Note:** Update Stream can be the TopN operation or the JOIN operation on two data streams.

-   Solution

    Set the asyncResultOrder parameter to ordered in the WITH clause for the dimension table.


