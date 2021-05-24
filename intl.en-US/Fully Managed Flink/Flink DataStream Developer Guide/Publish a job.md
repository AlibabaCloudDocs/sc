---
keyword: [publish, publish a job, DataStream, Table API]
---

# Publish a job

This topic describes how to publish DataStream API jobs and Table API jobs to clusters for running in fully managed Flink.

## Upload a JAR package

Before you run a DataStream API job or a Table API job, perform the following steps to upload the required JAR package to the development console of fully managed Flink.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Artifacts**.

4.  In the upper-right corner of the page, click **Upload Artifact** and select the JAR package that you want to upload.


## Create a job

1.  Log on to the development console of fully managed Flink to create a job.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Draft Editor**.

    4.  Click **New**.

    5.  In the **New Draft** dialog box, enter the job configuration information.

        Select STREAM / JAR for Type.

        |Parameter|Description|
        |---------|-----------|
        |**Name**|The name of the job that you want to create. **Note:** The job name must be unique in the project. |
        |**Type**|The following job types are supported:        -   SQL
        -   JAR
        -   PYTHON |
        |**Deployment Target**|The cluster in which the job is deployed. You must select a cluster mode before you can select a cluster. Two cluster modes are supported: **Per-Job Clusters** and **Session Clusters**. Per-Job Clusters is the default cluster mode.|

    6.  Click **OK**.

2.  On the Drafts page, enter the basic configuration information.

    You can directly enter the following configuration information. You can also click **YAML** in the lower-right corner of the page to directly modify the configuration information. The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Deployment Target|You can change the cluster that you selected when you created the job to another one.|
    |JAR URI|Select a file or manually upload a new file. You can drag the file that you want to upload to this field or click the ![Upload](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9217381261/p247547.png) icon on the right to select the file that you want to upload.|
    |Entrypoint Class|The entry class of the program. **Note:** If you do not specify a main class for the JAR package, enter a standard path in the **Entrypoint Class** field. |
    |Entrypoint main args|You can pass parameters and call them in the main method.|
    |Additional Dependencies|If you want to add more dependency files, select or enter valid addresses of files. By default, after you upload a Java dependency file, the file is saved in the oss://ossBucketName/artifacts/namespaces/namespaceName/ directory of the node that runs the job. |
    |Parallelism|The number of jobs that run in parallel.|

3.  On the right side of the Drafts page, click the **Advanced** tab and enter the configuration information based on your business requirements.

    The following table describes the parameters.

    |Section|Parameter|Description|
    |-------|---------|-----------|
    |**Configuration**|Flink Version|Valid values: 1.10, 1.11, and 1.12.|
    |Flink Image Tag|Select a Flink image tag.|
    |**Behavior**|Restore Strategy|The policy that restores the status of a job after the job is changed to the RUNNING state. Valid values:    -   Latest Savepoint: Restore the job from the latest savepoint.
    -   Latest State: Restore the job from the latest savepoint or checkpoint.
    -   None: Restore the job without the state. After the job is restored, the job is stateless and the calculation needs to be restarted.
**Note:** |
    |Max Job Creation Attempts|The number of retries after the instance fails to be created.|
    |Stop with Drain|If you turn on Stop with Drain, the existing data results in the window are returned when you manually terminate a job or perform a stateful upgrade for a job, regardless of whether the conditions to close the window are met.|
    |**Flink Configuration**|Checkpointing Interval|The interval at which a checkpoint is run on a scheduled basis. If you leave this parameter empty, the checkpointing feature is disabled.|
    |Min Time Between Checkpoints|The minimum interval between two checkpoints. If the maximum parallelism of checkpoints is one, this parameter specifies the minimum interval between two checkpoints.|
    |Retention Policy|Specifies whether to retain the last checkpoint if the job cannot be restarted or the job is suspended. Valid values:    -   Always: Retain the last checkpoint if the job is stopped.
    -   Only when FAILED: Delete the last checkpoint if the job is stopped.
    -   Never: The last checkpoint is not retained. This is the default value. |
    |Enabled Unaligned Checkpoints|If you turn on Enabled Unaligned Checkpoints, the running time of a checkpoint is significantly reduced during backpressure. However, this increases the state size of a single checkpoint.|
    |Flink Restart Strategy Configuration|If a task fails and checkpointing is disabled, JobManager cannot be restarted. If checkpointing is enabled, JobManager is restarted. Valid values:    -   Failure Rate: JobManager is restarted if the number of failures within the specified interval exceeds the upper limit.
    -   Fixed Delay: JobManager is restarted at a fixed interval.
    -   No Restarts: JobManager is not restarted. This is the default value. |
    |Flink Master Failover Configuration \('High Availability'\)|Valid values:    -   None: The system does not save metadata that is required when JobManager is restarted. This is the default value.
    -   Kubernetes: The system saves metadata to the Kubernetes cluster for JobManager to restart. |
    |Additional Configuration|Other Flink settings, such as `taskmanager.numberOfTaskSlots: 1`.|
    |**Resources**|Number of Task Managers|By default, the value of this parameter is the same as the value of Parallelism.|
    |Job Manager CPUs|Default value: 1.|
    |Job Manager Memory|The minimum value is 500 MiB. We recommend that you use GiB or MiB as the unit. For example, you can set this parameter to 1024 MiB or 1.5 GiB.|
    |Task Manager CPUs|Default value: 1.|
    |Task Manager Memory|The minimum value is 1 GiB. We recommend that you use GiB or MiB as the unit. For example, you can set this parameter to 1024 MiB or 1.5 GiB.|
    |**Logging**|Root Log Level|Valid values: TRACE, DEBUG, INFO, WARN, and ERROR.|
    |Log Levels|Enter the log name and log level.|
    |Logging Profile|The log template. You can use the system template or configure a custom template.|

4.  In the upper-right corner of the Drafts page, click **Publish**.


