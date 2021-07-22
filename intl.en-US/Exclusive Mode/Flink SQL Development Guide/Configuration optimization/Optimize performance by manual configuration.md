# Optimize performance by manual configuration

You can optimize the performance of a Realtime Compute for Apache Flink job by adjusting the settings of job, resource, and upstream and downstream storage parameters.

## Overview

You can configure the following three types of parameters to optimize job performance:

-   Upstream and downstream storage parameters
-   Job parameters, such as miniBatch
-   Resource parameters, such as parallelism, core, and heap\_memory

This topic describes how to configure the preceding three types of parameters. After you reconfigure parameters for a job, you must **terminate** and then **start** the job, or **suspend** and then **resume** the job to apply new settings. For more information, see [Apply new configurations](#section_isd_5nm_bgb).

## Upstream and downstream storage parameters

In Realtime Compute for Apache Flink, each data record can trigger read and write operations on the source and result tables. This affects the performance of upstream and downstream storage resources. To address this performance issue, you can configure batch size parameters to specify the number of data records that can be read from a source table or written to a result table at a time. The following table lists source and result tables that support batch size parameters.

|Table|Parameter|Description|Value|
|-----|---------|-----------|-----|
|DataHub source table|batchReadSize|The number of data records that are read at a time.|Optional. Default value: 10.|
|DataHub result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 300.|
|Log Service source table|batchGetSize|The number of log items that are read from a log group at a time.|Optional. Default value: 100.|
|AnalyticDB for MySQL V2.0 result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 1000.|
|ApsaraDB RDS result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 4096.|
|HybridDB for MySQL result table|batchSize|The number of data records that are written at a time.|Optional. Default value: 1000. The recommended maximum value is 4096.|
|bufferSize|The buffer size after duplicates are removed. You can use this parameter only after a primary key is defined.|Optional. This parameter is required when the batchSize parameter is configured. The recommended value for the bufferSize parameter is 4096.|

**Note:** To configure the batch data read and write feature, you can add the preceding parameters to the WITH clause in a data definition language \(DDL\) statement for a storage system. For example, add `batchReadSize='<number>'` to the WITH clause.

## Job parameters

The miniBatch parameter can be used only to optimize the GROUP BY operator. If you use Flink SQL to process streaming data, Realtime Compute for Apache Flink reads state data each time a data record arrives. This consumes a large number of I/O resources. If you configure the miniBatch parameter, Realtime Compute for Apache Flink uses the same key to read the state data only once for the data records and generates only the latest data record. This reduces the frequency to read the state data and minimizes downstream data updates. Configure the miniBatch parameter based on the following rules:

-   After you add parameters for a job, **terminate** and then **start** the job to apply the new settings.
-   After you change parameter settings for a job, **suspend** and then **resume** the job to apply the new settings.

```
# Enable window miniBatch in Realtime Compute for Apache Flink V3.2 and later. By default, window miniBatch is disabled in Realtime Compute for Apache Flink V3.2 and later. 
sql.exec.mini-batch.window.enabled=true
# Exactly-once semantics. 
blink.checkpoint.mode=EXACTLY_ONCE
# The checkpoint interval. Unit: milliseconds. 
blink.checkpoint.interval.ms=180000
blink.checkpoint.timeout.ms=600000
# Use Niagara as the state backend to configure time-to-live (TTL) for the state backend in Realtime Compute for Apache Flink V2.0 and later. The unit of TTL is milliseconds. 
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000
# In Realtime Compute for Apache Flink V2.0 and later, enable micro-batch processing that is performed at an interval of 5 seconds. You cannot configure this parameter when you use a window function. 
blink.microBatch.allowLatencyMs=5000
# The latency that is allowed for a job. 
blink.miniBatch.allowLatencyMs=5000
# Enable miniBatch for the node that joins two streams. 
blink.miniBatch.join.enabled=true
# The size of a batch. 
blink.miniBatch.size=20000
# Enable local aggregation. By default, this feature is enabled in Realtime Compute for Apache Flink V2.0 and later. You must manually enable Realtime Compute for Apache Flink V1.6.4. 
blink.localAgg.enabled=true
# Enable partial aggregation to improve efficiency when you run the CountDistinct function in Realtime Compute for Apache Flink V2.0 and later. 
blink.partialAgg.enabled=true
# Enable UNION ALL for optimization. 
blink.forbid.unionall.as.breakpoint.in.subsection.optimization=true
# Configure garbage collection for optimization. You cannot configure this parameter when you use a Log Service source table. 
blink.job.option=-yD heartbeat.timeout=180000 -yD env.java.opts='-verbose:gc -XX:NewRatio=3 -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:ParallelGCThreads=4'
# Specify the time zone. 
blink.job.timeZone=Asia/Shanghai
```

## Resource parameters

To optimize resource configurations, perform the following steps:

1.  Issue analysis
    1.  In the following topology, the percentage of the input queues at Task node 2 reaches 100%. The data at Task node 2 is stacked up and causes backpressure on Task node 1. At Task node 1, the percentage of the output queues has reached 100%.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4421659951/p33889.png)

    2.  Click Task node 2.
    3.  In the Vertex Topology section of the Overview tab, click the **SubTask List** tab. Then, find the subtask in which the value of **In Queue** is `100%`.
    4.  Click **View Logs** in the **Actions** column.
    5.  Click **Link to TaskExecutor**.
    6.  On the **TaskExecutor** tab, click the **Metrics Graph** tab to view the CPU utilization and memory usage.
2.  Performance optimization
    1.  On the right side of the job editing page, click the **Configurations** tab to view the details about resource configurations.
    2.  On the page that appears, change the parameter values of one or more operators in a group.
        -   To change the parameter values of one operator, perform the following steps:
            1.  In the **GROUP** box, click the plus sign \(+\) in the upper-right corner.
            2.  Move the pointer over the Operator box.
            3.  Click the ![Edit icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8041836061/p129375.png) icon next to the operator name.
            4.  In the **Modify Operator Data** dialog box, change the parameter values and click OK.
        -   To change the parameter values of multiple operators in a group at a time, perform the following steps:
            1.  Move the pointer over the **GROUP** box.
            2.  Click the ![Edit icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8041836061/p129375.png) icon next to **GROUP**.
            3.  In the **Modify Operator Data** dialog box, change the parameter values based on your business requirements and click OK.
    3.  In the upper-right corner of the **Configurations** page, choose **Configurations** \> **Apply**.
        -   If the job performance is not significantly improved after you change the values of the resource parameters for the group, perform the following steps to troubleshoot the issue:
            1.  Check whether data skew exists on the operator.
            2.  Check whether subtasks of complex operators, such as GROUP BY, WINDOW, and JOIN, are running normally.
        -   To remove an operator from a chain, perform the following steps:
            1.  Click the operator that you want to remove.
            2.  In the Modify Operator Data dialog box, set chainingStrategy to `HEAD`.

                If the chainingStrategy parameter of this operator is set to `HEAD`, you must also set the chainingStrategy parameter to `HEAD` for the next operator. The following table lists the valid values of the chainingStrategy parameter.

                |Value|Description|
                |-----|-----------|
                |**ALWAYS**|Operators are combined to increase the parallelism and optimize job performance.|
                |**NEVER**|Operators are not combined with the upstream and downstream operators.|
                |**HEAD**|Operators are combined with only the downstream operators.|

3.  Rules and suggestions
    -   We recommend that you set `core:heap_memory` to 1:4. This indicates that each CPU core is assigned with 4 GB of memory. Example:

        -   If the core parameter of operators is set to 1 and the heap\_memory parameter of the operator is set to 3, the system assigns 1 compute unit \(CU\) and 4 GB of memory to the chain.
        -   If the core parameter of operators is set to 1 and the heap\_memory parameter of operators is set to 5, the system assigns 1.25 CUs and 5 GB of memory to the chain.
        **Note:**

        -   The total number of cores for an operator is calculated by using the following formula: Value of the parallelism parameter × Value of the core parameter.
        -   The total heap\_memory size for an operator is calculated by using the following formula: Value of the parallelism parameter × Value of the heap\_memory parameter.
        -   The core value for a chain is the maximum core value among the operators in the group. The heap\_memory size for a chain is the total heap\_memory size of all the operators in the chain.
    -   **parallelism**
        -   Source node

            The number of source nodes is a multiple of the number of upstream partitions. For example, if the number of source nodes is 16, you must set the parallelism parameter to a divisor of 16, such as 16, 8, or 4. The divisor must exclude 16.

            **Note:** The value of the parallelism parameter for the source nodes cannot exceed the number of shards for the source nodes.

        -   Operator node

            Specify the parallelism parameter of the operator nodes based on the estimated queries per second \(QPS\).

            -   If the QPS is low, you can set the number of operator nodes to the value that is the same as the parallelism of the source nodes.
            -   If the QPS is high, make sure that the number of operator nodes is greater than the parallelism of the source nodes. For example, if the parallelism is 16, set the number of operator nodes to a value that is greater than 16, such as 64, 128, or 256.
        -   Sink node

            Set the parallelism parameter of the sink nodes to a value that is two to three times the number of downstream partitions.

            **Note:** Do not set the parallelism parameter of the sink nodes to a value that is greater than three times the number of downstream partitions. Otherwise, write timeout or failures may occur. For example, if the number of sink nodes is 16, do not set the parallelism parameter of these sink nodes to a value that is greater than 48.

    -   **core**

        This parameter specifies the number of CPU cores. You can specify this parameter based on the actual CPU utilization. The recommended value of this parameter is 0.25. The default value is 0.1.

    -   **heap\_memory**

        The heap memory size. You can configure this parameter based on the actual memory usage. The default value is 256. The unit is MB.

    -   **state\_size**

        You must set the **state\_size** parameter to `1` for task nodes where the GROUP BY, undefinedJOIN, OVER, or WINDOW operators are used. This way, the system assigns extra memory for the operator to access state data. The default value of the **state\_size** parameter is 0.

        **Note:** If you do not set **state\_size** to `1`, the job may fail.


## Apply new configurations

After you configure the parameters, we recommended that you **suspend** and then **resume** the job, but not **terminate** and then **start** the job. This ensures that the configurations take effect. The job status is cleared when the job is terminated. This may change execution results.

**Note:**

-   You can **suspend** and then **resume** a job after you change values of the resource parameters, parameters in the WITH clause, or job parameters.
-   You can **terminate** and then **start** a job after you modify the SQL logic, change the job version, add parameters to the WITH clause, or add job parameters.

After you restart or resume the job, you can navigate to **Administration** \> **Overview** \> **Vertex Topology**. Then, check whether the new configurations have taken effect.

-   To **suspend** and **resume** a job, perform the following steps:
    1.  Publish a job. For more information, see [Publish a job](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data development/Publish a job.md). Set **Resource Configuration Method** to **Use Latest Manually Configured Resources**.
    2.  On the **Administration** page, find the job that you want to suspend, and click **Suspend** in the Actions column.
    3.  On the **Administration** page, find the job that you want to resume, and click **Resume** in the Actions column.
    4.  In the **Resume** dialog box, click **Latest Configuration**.

        ![resume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5421659951/p35718.png)

-   To **terminate** and then **start** a job, perform the following steps:
    1.  Terminate a job.
        1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
        2.  In the top navigation bar, click **Administration**.
        3.  On the Administration page, find the job that you want to terminate, and click **Terminate** in the **Actions** column.
    2.  Start the job.
        1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
        2.  In the top navigation bar, click **Administration**.
        3.  On the Administration page, find the job that you want to start, and click **Start** in the **Actions** column.
        4.  In the **Start** dialog box, specify **Start Time for Reading Data**.![Trigger](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5421659951/p41326.png)
        5.  Click **OK**. The job is started.

            **Note:** Start Time for Reading Data specifies the time when Realtime Compute for Apache Flink starts to read data from the source table.

            -   If you select the current time, Realtime Compute for Apache Flink reads data that is generated after the current time.
            -   If you select a previous time, Realtime Compute for Apache Flink reads data that is generated from the selected time. This is used to trace historical data.

## Parameters

-   Global

    isChainingEnabled specifies whether chaining is enabled. The default value is true. Use the default value for this parameter.

-   Nodes

    |Parameter|Description|Allow modification|
    |---------|-----------|------------------|
    |id|The unique ID of the node. The node ID is generated by the system.|No|
    |uid|The unique user identifier \(UID\) of the node. The UID is used to generate the operator ID. If you do not specify this parameter, the UID is the same as the node ID.|No|
    |pact|The node type, such as data source, operator, or data sink.|No|
    |name|The name of the node. You can customize this parameter.|Yes|
    |slotSharingGroup|Specifies whether subtasks can share the same slot. Use the default value for this parameter.|No|
    |chainingStrategy|Defines the operator chaining strategy. If an operator is combined with an upstream operator, they run in the same thread. They are combined into an operator chain that has multiple running steps. The following items describe the three values for this parameter:     -   ALWAYS: Operators are combined to increase the parallelism and optimize job performance.
    -   NEVER: Operators are not combined with the related upstream or downstream operators.
    -   HEAD: Operators are combined with only the downstream operators.
|Yes|
    |parallelism|The number of parallel jobs on the node. You can increase the value based on your business requirements. Default value: 1.|Yes|
    |core|The number of CPU cores. You can specify this parameter based on the actual CPU utilization. Default value: 0.1. Recommended value: 0.25.|Yes|
    |heap\_memory|The heap memory size. You can specify this parameter based on the memory size that needs to be used. Default value: 256 MB.|Yes|
    |direct\_memory|The non-heap memory of a Java Virtual Machine \(JVM\). Unit: MB. Default value: 0.|You can change the value of this parameter, but we recommend that you use the default value.|
    |native\_memory|The JVM non-heap memory that is used for the Java Native Interface \(JNI\). The recommended value is 10. Unit: MB. Default value: 0.|You can change the value of this parameter, but we recommend that you use the default value.|

-   Chain

    A Flink SQL task is a directed acyclic graph \(DAG\) that contains multiple nodes or operators. Some upstream and downstream operators can be combined into a new operator when the operators run in the same thread. This process is known as a chain. As a result, the total number of CPU cores for the new operator is the maximum number of CPU cores among all the operators in the chain. The memory size for the chain equals the total memory size of all the operators in the chain. An operator chain can significantly reduce data transmission costs.

    **Note:**

    -   Only operators that have the same parallelism value can be combined to form a chain.
    -   You cannot add a GROUP BY operator to a chain.

