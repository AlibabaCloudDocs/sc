---
keyword: [parallelism of jobs, resource configuration, Autopilot]
---

# Configure Autopilot

This topic describes how to configure Autopilot and the considerations during the configuration.

In most cases, you need to spend a large amount of time on job tuning. For example, when you publish a job, you need to consider how to configure job information, such as the resources, the number of concurrent jobs, and the number and size of Task Managers. In addition, when a job is running, you also need to consider how to adjust the resources of the job to maximize the resource utilization for the job. If backpressure occurs for the job or the latency is increased, you need to consider how to adjust job configurations.

The **Autopilot** feature of fully managed Flink helps you more appropriately adjust the parallelism and resource configurations for your jobs and globally optimize your jobs based on the prerequisite. The prerequisite is that the performance of each operator and the upstream and downstream of streaming jobs meets the standards and is stable. The feature also solves various performance tuning problems, such as insufficient job throughput, backpressure in the entire link, and a waste of resources.

Although the **Autopilot** feature can improve the performance of streaming jobs, it cannot completely eliminate the performance bottleneck of the streaming jobs. You need to resolve the following issues by yourself:

-   Performance issues of upstream and downstream storage
    -   Upstream storage: for example, insufficient DataHub partitions or insufficient throughput of Message Queue \(MQ\)
    -   Downstream storage: for example, sink performance issues or ApsaraDB RDS deadlocks
-   Performance issues of user-defined functions \(UDFs\)

    Example: performance issues of scalar UDFs, user-defined aggregate functions \(UDAFs\), or user-defined table-valued functions \(UDTFs\)


## Prerequisites

The following conditions must be met before **Autopilot** configurations take effect:

-   The **Parallelism** parameter of the job is not specified in job code.

    If you compile the job by using the DataStream API or the Table API, make sure that the **Parallelism** parameter of the job is not specified in job code. Otherwise, the **Autopilot** feature cannot adjust the resources of the job.

-   The **Number of Task Managers** parameter is not specified on the interface or in code.

    If the **Number of Task Managers** parameter is specified on the interface or in code, **Autopilot** cannot run as expected.

-   The taskmanager.numberOfTaskSlots parameter is not specified in the **YAML** configuration file.
-   The **Upgrade Strategy** parameter is not set to None.

    If the **Upgrade Strategy** parameter is set to None, the system does not automatically restart the job after you modify job configurations. As a result, the **Autopilot** configurations cannot take effect.


## Considerations

-   After Autopilot is triggered, the job must be restarted. As a result, the job temporarily stops processing data. The way of restarting the job is determined by the Upgrade Strategy parameter. We recommend that you use the Stateless upgrade strategy.
-   In Autopilot policies, the processing modes of jobs are based on specific assumptions. For example, traffic smoothly changes, no data skew exists, and the throughput of each operator can linearly increase as the parallelism increases. If business logic seriously deviates from the preceding assumptions, job exceptions may occur. For example, the operation of changing the parallelism fails to be triggered. The job fails to reach the normal status. The job continuously restarts. In this case, you must adjust the mode of Autopilot to Monitoring or Disabled and perform manual tuning.
-   Autopilot cannot identify the issues of external systems. If the issues occur, you must resolve the issues of external systems by yourself. For example, if an external system becomes faulty or access slows down, the parallelism of the job increases. This increases the pressure on the external system and causes a breakdown of the external system.
-   Autopilot is not supported for jobs that are deployed in Session clusters.

## Enable Autopilot

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **Deployments**.

4.  Click the name of the desired deployment.

5.  Click the **Autopilot** tab.

6.  On the **Status** tab, click **Configure Now**.

7.  Select **Monitoring** or **Active** as the mode of Autopilot.

    -   **Disabled**

        The **Autopilot** feature is disabled.

    -   **Monitoring**

        The **Autopilot** feature is enabled. The system provides the recommended tuning configurations for the job based on the tuning policy that you select. You must click **Apply Recommendation** to make the configurations take effect.

    -   **Active**

        The **Autopilot** feature is enabled. The system provides the recommended tuning configurations for the job based on the tuning policy that you select. The system automatically **starts** the job to make the configurations take effect. No further action is required.

8.  Select a tuning policy.

    **Note:** We recommend that you use the following tuning policies:

    -   Use the **CPU-Based** policy alone.
    -   Use the **Source-Delay-Based** policy in conjunction with the **Slot-Utilization-Based** policy.
    -   **CPU-Based** \(default tuning policy\)
        -   Policy description

            The **Parallelism** parameter of the job is optimized based on the actual CPU usage of the Task Manager. This improves throughput and reduces costs. The parallelism of the current job can be increased \(Scale Up\) and decreased \(Scale Down\).

        -   Tuning scenarios
            -   CPU-intensive jobs \(We strongly recommend that you use the CPU-Based policy in this scenario.\)
            -   General job scenarios.
        -   Limits
            -   If your job is an I/O-intensive job, we recommend that you do not configure the **CPU-Based** policy for tuning.
            -   We recommend that you do not enable the **CPU-Based** and **Slot-Utilization-Based** policies at the same time.
        -   Parameter description

            |Parameter|Description|Default value|Explanation of the default value|
            |---------|-----------|-------------|--------------------------------|
            |cpu-based.scale-up.threshold|The CPU usage threshold that triggers the Scale Up operation.|0.8|If the CPU usage of the Task Manager exceeds 80%, the Scale Up operation is triggered.|
            |cpu-based.scale-down.threshold|The CPU usage threshold that triggers the Scale Down operation.|0.2|If the CPU usage of the Task Manager is lower than 20%, the Scale Down operation is triggered.|
            |cpu-based.scale-up.window-size.min|The size of the time window for collecting CPU usage statistics during Scale Up.|3. Unit: min|The CPU usage in the last 3 minutes is calculated. If the CPU usage exceeds 80% within the last 3 minutes, the Scale Up operation is triggered.|
            |cpu-based.scale-down.window-size.min|The size of the time window for collecting CPU usage statistics during Scale Down.|30. Unit: min|The CPU usage in the last 30 minutes is calculated. If the CPU usage is lower than 20% within the last 30 minutes, the Scale Down operation is triggered.|

    -   **Source-Delay-Based**

        **Note:** Assume that your data source is Log Service or DataHub and the tuning effect is unsatisfactory after the **CPU-Based** policy is used. You can use the Source-Delay-Based policy for dynamic Scale Up.

        -   Policy description

            The throughput processing capabilities of a job are measured based on the latency of consumption source data. If the data processing capability is insufficient, you can perform the Scale Up operation to improve the throughput of the job.

        -   Limits
            -   This policy supports only Scale Up for jobs and does not support Scale Down for jobs.
            -   This policy takes effect for only Log Service, DataHub, and Kafka, but not for other connectors.
        -   Parameter description

            |Parameter|Description|Default value|Explanation of the default value|
            |---------|-----------|-------------|--------------------------------|
            |source-delay-based.threshold|The maximum latency threshold that can be tolerated.|60000. Unit: ms|If the latency of the job reaches 60,000 ms, the Scale Up operation is triggered.|
            |source-delay-based.target-utilization|The desired resource utilization.|0.8|The desired resource utilization of the job is 80%. If the resource utilization reaches 80%, the Scale Up operation is stopped.|
            |source-delay-based.scale-up.window-size.min|The size of the statistical period window for the latency change rate.|3. Unit: min|The latency of the job and the value of `slot-utilization` in the last 3 minutes are calculated. If the threshold is reached, the Scale Up operation is triggered.|

    -   **Slot-Utilization-Based**
        -   Policy description

            The idle time of data processing nodes \(excluding the Source node\) is monitored. If the idle time is continuously greater than a specific threshold, the parallelism of the job is decreased to reduce resource utilization.

        -   Limits
            -   If the Source node becomes the performance bottleneck of a job, you must disable the policy.

                **Note:** If the Source node is not the performance bottleneck of the job, you can use the **Slot-Utilization-Based** policy for dynamic Scale Down.

            -   This policy supports only Scale Down and does not support Scale Up.
            -   We recommend that you do not enable this policy and the **CPU-Based** policy at the same time.
        -   Parameter description

            |Parameter|Description|Default value|Explanation of the default value|
            |---------|-----------|-------------|--------------------------------|
            |slot-utilization-based.threshold|The `slot-utilization` that triggers the Scale Down operation.|0.5|If `slot-utilization` is lower than 50%, the Scale Down operation is triggered.|
            |slot-utilization-based.scale-down.window-size.min|The size of the window for collecting `slot-utilization` statistics.|30. Unit: min|The default time window for calculating `slot-utilization` is 30 minutes.|

    -   **Memory-Utilization-Based**
        -   Policy description

            The resource size is adjusted based on the memory usage of the Task Manager, the garbage collection \(GC\), and the GC situation of the Job Manager.

        -   Parameter description

            |Parameter|Description|Default value|Explanation of the default value|
            |---------|-----------|-------------|--------------------------------|
            |memory-utilization-based.memory-usage-max.threshold|The maximum memory usage that can be tolerated.|0.95|If the memory usage of the Task Manager exceeds 95%, the operation of scaling up the memory is triggered.|
            |memory-utilization-based.memory-usage-min.threshold|The minimum memory usage that can be tolerated.|0.3|If the memory usage of the Task Manager is lower than 30%, the operation of scaling down the memory is triggered.|
            |memory-utilization-based.memory-usage.target-utilization|The desired resource utilization.|0.8|After the memory of the Task Manager is adjusted, the desired memory usage reaches 80%.|
            |memory-utilization-based.scale-up.window-size.min|The monitoring cycle of memory Scale Up.|3. Unit: min|The memory usage in the last 3 minutes is calculated. If the memory usage exceeds 95%, the operation of scaling up the memory is triggered.|
            |memory-utilization-based.scale-down.window-size.min|The monitoring cycle of memory Scale Down.|30. Unit: min|The memory usage in the last 30 minutes is calculated. If the memory usage is lower than 30%, the operation of scaling down the memory is triggered.|
            |memory-utilization-based.gc-ratio.threshold|The GC frequency threshold that triggers memory Scale Up.|0.2|If the number of GC times per second exceeds 0.2, the operation of scaling up the memory is triggered.**Note:** Ververica Platform \(VVP\) 2.2.2 does not support GC-based tuning. |
            |memory-utilization-based.gc-time-longest-ms.threshold|The time threshold of a single GC that triggers memory Scale Up.|15000. Unit: ms|If the time of a single GC is greater than 15s, the operation of scaling up the memory is triggered.**Note:** VVP 2.2.2 does not support GC-based tuning. |
            |memory-utilization-based.gc-time-ms-per-second.threshold|The average GC time per second during data processing. If the value is exceeded, memory Scale Up is triggered.|200. Unit: ms|If the average GC time per second exceeds 200 ms during data processing, the memory is scaled up.**Note:** VVP 2.2.2 does not support GC-based tuning. |
            |memory-utilization-based.memory-scale-up.max|The maximum value to which the memory of a single Task Manager can be adjusted.|16Gi|The memory of the Task Manager can be adjusted to 16Gi at most.**Note:** VVP 2.2.2 does not support GC-based tuning. |
            |memory-utilization-based.memory-scale-up.ratio|The multiple by which the memory is scaled up if the GC rate and the GC time exceed the thresholds. Among which:            -   The GC rate indicates the average number of GC times per second when JVM is running.
            -   The GC time indicates the longest time that is spent on a single GC when JVM is running.
|1.5|The ratio by which the memory is scaled up if the GC rate or the GC time exceeds the threshold. By default, the memory is scaled up by 1.5 times.**Note:** VVP 2.2.2 does not support GC-based tuning. |

    -   **job-exception-based**
        -   Policy description

            Resources are adjusted based on the failover time of the job and exception information.

        -   Parameter description

            |Parameter|Description|Default value|Explanation of the default value|
            |---------|-----------|-------------|--------------------------------|
            |job-exception-based.oom-exception.memory-scale-up.ratio|The ratio of the Task Manager memory that is scaled up if an OOM\(OutOfMemory\) exception occurs.|1.5|If an OOM exception occurs, the desired memory is adjusted to 1.5 times the current memory.|
            |job-exception-based.oom-exception.memory-scale-up.max|The maximum value to which the memory of a single Task Manager can be adjusted.|16Gi|The memory of the Task Manager can be adjusted to 16Gi at most.|
            |job-exception-based.oom-exception.include-tm-timeout|Specifies whether the OOM exception of the Task Manager includes Task Manager time-out.|true|By default, the OOM exception of the Task Manager includes Task Manager time-out.|

9.  Specify **Cooldown**.

    The cooldown time refers to the minimum time interval between two automatic job startups that are performed by **Autopilot**.

    If the mode of **Autopilot** is set to Active, **Autopilot** automatically **starts** the job based on the recommended configurations.

10. Specify **Additional Configuration**.

    Assume that the default value of **CPU-Based**, **Source-Delay-Based**, or **Slot-Utilization-Based** does not meet your job tuning expectations. You can customize the value of the corresponding parameter in Additional Configuration after you select the corresponding tuning policy. When you configure multiple parameters, write each parameter in a single row, as shown in the following example:

    ```
    slot-utilization-based.threshold: 1
    slot-utilization-based.scale-down.window-size.min: 50
    ```

    **Note:** One space must exist between the colon \(:\) followed by the preceding parameter and the parameter value. The configuration cannot be saved if the space is missing.

    You can also limit the maximum and minimum values to which the parallelism can be adjusted. The following parameters are described.

    |Parameter|Description|Default value|Explanation of the default value|
    |---------|-----------|-------------|--------------------------------|
    |parallelism.scale.max|The maximum parallelism limit when the parallelism is increased.|-1|By default, the maximum parallelism is not limited when the parallelism is increased.|
    |parallelism.scale.min|The minimum parallelism limit when the parallelism is decreased.|1|When you decrease the parallelism, the minimum parallelism is 1.|

11. Click **Save**.


