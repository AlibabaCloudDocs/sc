# Configure a session cluster

You can deploy or debug jobs in a session cluster. This improves the resource utilization of JobManager and spins up the job startup.

Fully managed Flink supports two cluster modes: Per-Job cluster mode and session cluster mode. The following list compares the two cluster modes.

-   Per-Job clusters: the default mode. Resources are isolated among jobs. Each job requires an independent JobManager. This causes low resource utilization of JobManager that runs a small job. Therefore, this mode is ideal for jobs that consume a large number of resources or jobs that run in a continuous and stable manner.
-   Session clusters: Multiple jobs can reuse the same JobManager. This improves the resource utilization of JobManager. Therefore, this mode is ideal for jobs that consume few resources or jobs that start and stop in a frequent manner.

    **Note:**

    -   You can configure multiple session clusters for each project. However, you can enable Use for SQL Editor previews for only one session cluster.
    -   You cannot enable the autopilot feature for a session cluster.
    -   Each session cluster that is running consumes additional 0.5 compute unit \(CU\) resources.

## Create a session cluster

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Administration** \> **Session Clusters**.

4.  In the upper-right corner of the page, click **Create Session Cluster**.

5.  Configure the parameters.

    ![Create a session cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8996604161/p187373.png)

    The following table describes the parameters.

    |Module|Parameter|Description|
    |------|---------|-----------|
    |Standard|Name|The name of the cluster.|
    |State|The desired running status of the current cluster. Valid values:    -   RUNNING: The cluster keeps running after it is configured.
    -   STOPPED: The cluster is stopped after it is configured, and the jobs in the cluster are also terminated. |
    |Use for SQL Editor previews|Specifies whether to use this session cluster for SQL previews.**Note:** You can enable Use for SQL Editor previews for only one session cluster. If you turn on this switch for the current cluster, the setting of another cluster for which this feature was enabled becomes invalid. |
    |Label key|You can configure labels for jobs in the Labels section. This allows you to find a job on the Overview page in an efficient manner.|
    |Label value|N/A|
    |Configuration|Flink Version|Only two major versions Flink 1.10 and Flink 1.11 are supported.**Note:** For Python API jobs, select Flink 1.11 or later. |
    |Flink Image Tag|Select a Flink image tag.**Note:** For Python API jobs, select 1.11.2-vvr-2.1.1 or later. |
    |Flink Restart Strategy Configuration|Valid values:    -   No Restarts: No jobs are restarted.
    -   Fixed Delay: A delayed restart at a fixed interval. If you select this option, you must also configure Number of Restart Attempts and Delay between Restart Attempts.
    -   Failure Rate: the failure rate. If you select this option, you must also configure Failure Rate Interval, Max Failures per Interval, and Delay between Restart Attempts.
**Note:** If you leave this parameter empty, the default Apache Flink restart strategy is used. In this case, if a task fails and checkpointing is disabled, JobManager is not restarted. If you enable checkpointing, JobManager is restarted. |
    |Additional Configuration|Configure other Flink settings. For example, `taskmanager.numberOfTaskSlots: 1`.|
    |Resources|Number of Task Managers|By default, the value is the same as the parallelism.|
    |Job Manager CPUs|Default value: 1.|
    |Job Manager Memory|Minimum value: 500Mi. We recommend that you use Gi or Mi as the unit. For example, 1024Mi or 1.5Gi.|
    |Task Manager CPUs|Default value: 1.|
    |Task Manager Memory|Minimum value: 1Gi. We recommend that you use Gi or Mi as the unit. For example, 1024Mi or 1.5Gi.|
    |Logging|Root Log Level|Valid values: TRACE, DEBUG, INFO, WARN, and ERROR.|
    |Logger name|Enter the log name.|
    |Logger level|Enter the log level.|
    |Logging Profile|The log template. You can use the system template or configure a custom template.|

6.  Click **Create Session Cluster**.

    Assume that the session cluster is created. In this case, when you create a DataStream job or deploy an SQL job, you can select the created session cluster from the **Deployment Target** drop-down list.

    ![Deployment Target](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8996604161/p187554.png)


