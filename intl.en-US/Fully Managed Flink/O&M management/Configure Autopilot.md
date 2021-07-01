---
keyword: [parallelism of jobs, resource configuration, Autopilot]
---

# Configure Autopilot

By default, Autopilot is enabled for fully managed Flink. This topic describes how to configure Autopilot and the precautions in the configuration process.

In most cases, you need to spend a large amount of time on job tuning. For example, when you publish a job, you need to consider how to configure job information, such as the resources, the number of parallel jobs, and the number and size of TaskManagers. When a job is running, you also need to consider how to adjust the resources of the job to maximize resource utilization. If backpressure occurs for the job or the latency increases, you must consider how to adjust job configurations.

If the performance of each operator and the upstream and downstream storage of streaming jobs meets business requirements and keeps stable, the Autopilot feature of fully managed Flink helps you more appropriately adjust the parallelism and resource configurations to globally optimize your jobs. This feature also resolves various performance issues, such as insufficient job throughput, backpressure in the entire link, and poor resource utilization.

Although the Autopilot feature can improve the performance of streaming jobs, it cannot completely eliminate the performance bottleneck of the streaming jobs. You need to resolve the following issues by yourself:

-   Performance issues of upstream and downstream storage
    -   Upstream storage: For example, the number of DataHub partitions or the throughput of Message Queue for Apache RocketMQ is insufficient.
    -   Downstream storage: For example, sink nodes have performance issues or ApsaraDB RDS has a deadlock.
-   Performance issues of user-defined extensions \(UDXs\)

    For example, performance issues of user-defined scalar functions \(UDFs\), user-defined aggregate functions \(UDAFs\), or user-defined table-valued functions \(UDTFs\) occur.


## Prerequisites

Before Autopilot configurations can take effect, make sure that the following conditions are met:

-   The Parallelism parameter is not specified in job code.

    The Parallelism parameter is not specified in the job code when you compile your job by using the DataStream API or the Table API. If this parameter is specified, the Autopilot feature cannot adjust the resources of the job.

-   The Number of TaskManagers parameter is not specified on the UI or in code.

    If the Number of TaskManagers parameter is specified on the UI or in code, Autopilot cannot run as expected.

-   The taskmanager.numberOfTaskSlots parameter is not specified in the YAML configuration file.

## Usage notes

-   After Autopilot is triggered, the job must be restarted. As a result, the job temporarily stops processing data.
-   In Autopilot policies, the processing modes of jobs are based on specific assumptions. For example, traffic smoothly changes, no data skew exists, and the throughput of each operator can linearly increase as the parallelism increases. If the business logic seriously deviates from the preceding assumptions, job exceptions may occur. For example, the operation of changing the parallelism fails to be triggered, the job runs abnormally, or the job continuously restarts. In this case, you must set the Mode parameter of Autopilot to Monitoring or Disabled and perform manual tuning.
-   Autopilot cannot identify the issues of external systems. If such an issue occurs, you must resolve the issue by yourself. For example, if an external system becomes faulty or access to the external system slows down, the parallelism of jobs increases. This increases the pressure on the external system. As a result, service avalanche occurs.
-   Autopilot is not supported for jobs that are deployed in session clusters.

## Configure Autopilot

1.  Go to the Deployments page.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

2.  Configure Autopilot.

    1.  Click the name of the job that you want to optimize.

    2.  Click the **Autopilot** tab.

    3.  Click the **Configuration** tab.

    4.  Autopilot is enabled by default. You can set the Mode parameter to adjust the setting of Autopilot. Valid values:

        -   **Active**

            If you set Mode to Active, Autopilot is enabled. The system provides the recommended tuning configurations for your job based on the tuning policy that you selected. The system automatically starts the job to make the configurations take effect. No further action is required.

        -   **Disabled**

            If you set Mode to Disabled, Autopilot is disabled.

        -   **Monitoring**

            This is the default value, which indicates that Autopilot is enabled. The system provides the recommended tuning configurations for your job based on the tuning policy that you selected. If you want to make the recommended configurations take effect, you can use one of the following methods:

            -   Change the value of Mode to **Active**.
            -   Manually modify the related configurations on the **Advanced** tab based on the information that appears in the **Recommendation** section on the **Status** tab.
    5.  Select a tuning policy.

        **Note:** In Ververica Platform \(VVP\) 2.4 or later, the Source-Delay-Based, Slot-Utilization-Based, and CPU-Based policies take effect at the same time. The system intelligently determines whether the value of the Parallelism parameter needs to be adjusted.

        -   **CPU-Based** \(default tuning policy\)
            -   Policy description

                The **Parallelism** parameter of the job is optimized based on the actual CPU utilization of the TaskManager. This improves throughput and reduces costs. The parallelism of the current job can be increased \(scale-up\) and decreased \(scale-down\).

            -   Scenarios
                -   CPU-intensive jobs. We strongly recommend that you use the CPU-Based policy in this scenario.
                -   General job scenarios.
            -   Limits

                If your job is an I/O-intensive job, we recommend that you do not configure the **CPU-Based** policy for tuning.

            -   Parameter description

                |Parameter|Description|Default value|Description of the default value|
                |---------|-----------|-------------|--------------------------------|
                |cpu-based.scale-up.threshold|The CPU utilization threshold that triggers the scale-up operation.|0.8|If the CPU utilization of the TaskManager exceeds 80%, the scale-up operation is triggered.|
                |cpu-based.scale-down.threshold|The CPU utilization threshold that triggers the scale-down operation.|0.2|If the CPU utilization of the TaskManager is less than 20%, the scale-down operation is triggered.|
                |cpu-based.scale-up.window-size.min|The size of the time window for collecting CPU utilization statistics during the scale-up operation.|3. Unit: minutes|The CPU utilization in the last 3 minutes is calculated. If the CPU utilization exceeds 80% within the last 3 minutes, the scale-up operation is triggered.|
                |cpu-based.scale-down.window-size.min|The size of the time window for collecting CPU utilization statistics during the scale-down operation.|30. Unit: minutes|The CPU utilization in the last 30 minutes is calculated. If the CPU utilization is less than 20% within the last 30 minutes, the scale-down operation is triggered.|

        -   **Source-Delay-Based**

            **Note:** If your data source is Log Service or DataHub and the tuning effect is unsatisfactory after the **CPU-Based** policy is used, you can use the Source-Delay-Based policy for dynamic scale-up.

            -   Policy description

                The throughput of a job is measured based on the latency of consuming source data. If the data processing capability is insufficient, you can perform the scale-up operation to improve the throughput of the job.

            -   Limits

                This policy supports only scale-up operations but does not support scale-down operations.

            -   Parameter description

                |Parameter|Description|Default value|Description of the default value|
                |---------|-----------|-------------|--------------------------------|
                |source-delay-based.threshold|The maximum latency threshold that is allowed.|60000. Unit: milliseconds|If the latency of the job reaches 60,000 ms, the scale-up operation is triggered.|
                |source-delay-based.scale-up.window-size.min|The size of the statistical period window based on the latency.|3. Unit: minutes|The latency of the job in the last 3 minutes is calculated. If the latency reaches the threshold, the scale-up operation is triggered.|

        -   **Slot-Utilization-Based**
            -   Policy description

                The value of `slot-utilization` in a specified time window is calculated. If the value of `slot-utilization` is continuously greater than the threshold of `slot-utilization` that triggers the scale-down operation, you can increase the parallelism of jobs to alleviate the resource shortage. If the value is continuously less than the threshold of `slot-utilization` that triggers the scale-up operation, you can decrease the parallelism of jobs to reduce resource consumption.

                **Note:** `slot-utilization` indicates how busy a task is. If the value of this parameter is 100%, the task processes data all the time.

            -   Limits

                The time during which the data of the source node is processed is not calculated in `slot-utilization`. If the source node becomes the performance bottleneck of a job, you must disable this policy.

                **Note:** If the source node is not the performance bottleneck of the job, you can use the **Slot-Utilization-Based** policy for dynamic scale-down.

            -   Parameter description

                |Parameter|Description|Default value|Description of the default value|
                |---------|-----------|-------------|--------------------------------|
                |slot-utilization-based.threshold|The threshold of `slot-utilization` that triggers the scale-down operation. If the value of `slot-utilization` is less than the value of this parameter, the scale-down operation is triggered.

|0.2|If the value of `slot-utilization` is less than 20%, the scale-down operation is triggered.|
                |slot-utilization-based.scale-down.window-size.min|The time window of `slot-utilization` that triggers the scale-down operation.|30. Unit: minutes|The default time window for triggering the scale-down operation is 30 minutes.|
                |slot-usage-detector.scale-up.threshold|The threshold of `slot-utilization` that triggers the scale-up operation. If the value of `slot-utilization` is greater than the value of this parameter, the scale-down operation is triggered.

|0.8|If the value of `slot-utilization` is greater than 80%, the scale-up operation is triggered.|
                |slot-usage-detector.scale-up.sample-interval|The time window of `slot-utilization` that triggers the scale-up operation.|3. Unit: minutes|The default time window for triggering the scale-up operation is 3 minutes.|

        -   **Memory-Utilization-Based**
            -   Policy description

                The resource size is adjusted based on the memory usage of the TaskManager, garbage collection \(GC\), and GC situation of the JobManager.

            -   Parameter description

                |Parameter|Description|Default value|Description of the default value|
                |---------|-----------|-------------|--------------------------------|
                |memory-utilization-based.memory-usage-max.threshold|The maximum memory usage that can is allowed.|0.95|If the memory usage of the TaskManager exceeds 95%, memory scale-up is triggered.|
                |memory-utilization-based.memory-usage-min.threshold|The minimum memory usage that can is allowed.|0.3|If the memory usage of the TaskManager is less than 30%, memory scale-down is triggered.|
                |memory-utilization-based.memory-usage.target-utilization|The expected memory usage.|0.8|After the memory of the TaskManager is adjusted, the expected memory usage reaches 80%.|
                |memory-utilization-based.scale-up.window-size.min|The monitoring cycle of memory scale-up.|3. Unit: minutes|The memory usage in the last 3 minutes is calculated. If the memory usage exceeds 95%, memory scale-up is triggered.|
                |memory-utilization-based.scale-down.window-size.min|The monitoring cycle of memory scale-down.|30. Unit: minutes|The memory usage in the last 30 minutes is calculated. If the memory usage is less than 30%, memory scale-down is triggered.|
                |memory-utilization-based.gc-ratio.threshold|The GC frequency threshold that triggers memory scale-up.|0.2|If the number of times GC is performed per second exceeds 0.2, memory scale-up is triggered. **Note:** VVP 2.2.2 does not support GC-based tuning. |
                |memory-utilization-based.gc-time-longest-ms.threshold|The time threshold of a single GC that triggers memory scale-up.|15000. Unit: milliseconds|If a single GC exceeds 15 seconds, memory scale-up is triggered. **Note:** VVP 2.2.2 does not support GC-based tuning. |
                |memory-utilization-based.gc-time-ms-per-second.threshold|The average GC time per second during data processing. If this threshold is exceeded, memory scale-up is triggered.|200. Unit: milliseconds|If the average GC time per second exceeds 200 ms during data processing, memory scale-up is triggered. **Note:** VVP 2.2.2 does not support GC-based tuning. |
                |memory-utilization-based.memory-scale-up.max|The maximum value to which the memory of a single TaskManager can be adjusted.|16 Gi|The maximum memory of the TaskManager can be adjusted to 16 GiB. **Note:** VVP 2.2.2 does not support GC-based tuning. |
                |memory-utilization-based.memory-scale-up.ratio|The multiple by which the memory is scaled up if the GC rate and the GC time exceed the specified thresholds.                 -   The GC rate indicates the average number of times GC is performed per second when Java Virtual Machine \(JVM\) is running.
                -   The GC time indicates the longest time that is spent on a single GC when JVM is running.
|1.5|The ratio by which the memory is scaled up if the GC rate or the GC time exceeds the threshold. By default, the memory is scaled up by 1.5 times. **Note:** VVP 2.2.2 does not support GC-based tuning. |

        -   **job-exception-based**
            -   Policy description

                Resources are adjusted based on the failover time of the job and exception information.

            -   Parameter description

                |Parameter|Description|Default value|Description of the default value|
                |---------|-----------|-------------|--------------------------------|
                |job-exception-based.oom-exception.memory-scale-up.ratio|The ratio of the TaskManager memory that is scaled up if an out of memory \(OOM\) exception occurs.|1.5|If an OOM exception occurs, the expected memory is adjusted to 1.5 times the current memory.|
                |job-exception-based.oom-exception.memory-scale-up.max|The maximum value to which the memory of a single TaskManager can be adjusted.|16 Gi|The maximum memory of the TaskManager can be adjusted to 16 GiB at most.|
                |job-exception-based.oom-exception.include-tm-timeout|Specifies whether the OOM exception of the TaskManager includes TaskManager timeout.|true|By default, the OOM exception of the TaskManager includes TaskManager timeout.|

    6.  Specify **Cooldown**.

        The cooldown time refers to the minimum time interval between two automatic job startups that are triggered by **Autopilot**.

        If **Mode** of **Autopilot** is set to **Active**, Autopilot automatically starts the job based on the recommended configurations.

    7.  Specify **Additional Configuration**.

        If the default value of **CPU-Based**, **Source-Delay-Based**, or **Slot-Utilization-Based** does not meet your job tuning expectations, you can customize the value of the related parameter in Additional Configuration after you select the required tuning policy. When you configure multiple parameters, write each parameter in a single row, as shown in the following example:

        ```
        slot-utilization-based.threshold: 1
        slot-utilization-based.scale-down.window-size.min: 50
        ```

        **Note:** A space is required between the colon \(:\) followed by the preceding parameter and the parameter value. If no space is used, the configuration cannot be saved.

        You can also limit the maximum and minimum values to which the parallelism can be adjusted. The following table describes the parameters.

        |Parameter|Description|Default value|Description of the default value|
        |---------|-----------|-------------|--------------------------------|
        |parallelism.scale.max|The maximum parallelism when the parallelism is increased.|-1|By default, the maximum parallelism is not limited when the parallelism is increased.|
        |parallelism.scale.min|The minimum parallelism limit when the parallelism is decreased.|1|When you decrease the parallelism, the minimum parallelism is 1.|

3.  Click **Save**.


