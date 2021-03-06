# Configure a session cluster

You can deploy or debug jobs in a session cluster. This improves the resource utilization of JobManager and spins up the job startup.

Fully managed Flink supports two cluster modes: **Per-Job Clusters** and **Session Clusters**. The following list compares the two cluster modes.

-   **Per-Job Clusters**: This is the default mode. Resources are isolated among jobs. Each job requires an independent JobManager. This causes low resource utilization of JobManager that runs a small job. Therefore, this mode is ideal for jobs that consume a large number of resources or jobs that run in a continuous and stable manner.
-   **Session Clusters**: Multiple jobs can reuse the same JobManager. This improves the resource utilization of JobManager. Therefore, this mode is ideal for jobs that consume few resources or jobs that start and stop in a frequent manner.

    **Note:**

    -   You can configure multiple session clusters for each project. However, you can enable Use for SQL Editor previews for only one session cluster.
    -   You cannot enable the autopilot feature for a session cluster.
    -   Each session cluster that is running consumes additional 0.5 compute unit \(CU\) resources.

## Create a session cluster

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Administration** \> **Session Clusters**.

4.  In the upper-right corner of the page, click **Create Session Cluster**.

5.  Configure the parameters.

    ![Create a session cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8996604161/p187373.png)

    The following table describes the parameters.

    |Module|Parameter|Description|
    |------|---------|-----------|
    |Standard|Name|The name of the cluster.|
    |State|The desired running status of the current cluster. Valid values:    -   STOPPED: The cluster is stopped after it is configured, and the jobs in the cluster are also terminated.
    -   RUNNING: The cluster keeps running after it is configured. |
    |Use for SQL Editor previews|Specifies whether to use this session cluster for SQL previews. **Note:** You can enable Use for SQL Editor previews for only one session cluster. If you turn on this switch for the current cluster, the setting of another cluster for which this feature was enabled becomes invalid. |
    |Label key|You can configure labels for jobs in the Labels section. This allows you to find a job on the Overview page in an efficient manner.|
    |Label value|None.|
    |Configuration|Flink Version|Valid values: 1.10, 1.11, and 1.12. **Note:** For Python API jobs, you must select 1.11 or later. |
    |Flink Image Tag|Select a Flink image tag. **Note:** For Python API jobs, select 1.11.2-vvr-2.1.1 or later. |
    |Flink Restart Strategy Configuration|Valid values:    -   No Restarts: No jobs are restarted.
    -   Fixed Delay: A delayed restart at a fixed interval. If you select this option, you must also configure Number of Restart Attempts and Delay between Restart Attempts.
    -   Failure Rate: the failure rate. If you select this option, you must also configure Failure Rate Interval, Max Failures per Interval, and Delay between Restart Attempts.
**Note:** If you leave this parameter empty, the default Apache Flink restart strategy is used. In this case, if a task fails and checkpointing is disabled, JobManager is not restarted. If you enable checkpointing, JobManager is restarted. |
    |Flink Master Failover Configuration \('High Availability'\)|Valid values:    -   Kubernetes: The Flink system saves the metadata to Kubernetes for use when a JobManager is restarting.
    -   None: The Flink system does not save metadata for use when JobManager is restarting. |
    |Additional Configuration|Configure other Flink settings. For example, `taskmanager.numberOfTaskSlots: 1`.|
    |Resources|Number of Task Managers|By default, the value of this parameter is the same as the value of Parallelism.|
    |Job Manager CPUs|Default value: 1.|
    |Job Manager Memory|Minimum value: 500 MiB. We recommend that you use GiB or MiB as the unit. For example, you can set this parameter to 1024 MiB or 1.5 GiB.|
    |Task Manager CPUs|Default value: 1.|
    |Task Manager Memory|Minimum value: 1 GiB. We recommend that you use GiB or MiB as the unit. For example, you can set this parameter to 1024 MiB or 1.5 GiB.|
    |Log configurations|Root Log Level|Valid values: TRACE, DEBUG, INFO, WARN, and ERROR.|
    |Logger name|Enter the log name.|
    |Logger level|Enter the log level.|
    |Logging Profile|The log template. You can use the system template or configure a custom template.|

6.  Click **Create Session Cluster**.

    After the session cluster is created, you can select the created session cluster from the **Deployment Target** drop-down list when you create a DataStream job or deploy an SQL job.

    ![Deployment Target](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8996604161/p187554.png)


