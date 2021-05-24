---
keyword: [fully managed Flink, develop, SQL job]
---

# Develop a job

This topic describes the limits on and the methods used to develop an SQL job in fully managed Flink.

## Limits

-   SQL jobs that are published by using the SQL editor support only Flink 1.11 and Flink 1.12.
-   For more information about upstream and downstream storage supported by SQL, see [Upstream and downstream storage](/intl.en-US/Fully Managed Flink/Overview/Upstream and downstream storage.md).

## Develop a job

To help you write and manage Flink SQL jobs, fully managed Flink offers you a set of Flink SQL features that make job development more efficient. You can use these features to manage metadata, register user-defined functions \(UDFs\), and use the SQL editor.

1.  Go to the fully managed Flink page to create a job.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Draft Editor**.

    4.  Click **New**.

    5.  In the **New Draft** dialog box, enter the job configurations.

        Select STREAM/SQL for Type.

        |Parameter|Description|
        |---------|-----------|
        |**Name**|The name of the job. **Note:** The job name must be unique in the current project. |
        |**Type**|The following job types are supported:        -   SQL
        -   JAR
        -   PYTHON |
        |**Deployment Target**|The cluster in which you want to deploy your job. **Per-Job Clusters** \(default\) and **Session Clusters** are supported. Differences between the two cluster modes:        -   **Per-Job Clusters**: The Per-Job cluster is selected by default. Resources in this cluster are isolated among jobs. Each job requires an independent JobManager, which causes low JobManager resource utilization for small jobs. Therefore, this type of cluster is suitable for jobs that consume many resources or jobs that run in a continuous and stable manner.
        -   **Session Clusters**: The Session cluster allows multiple jobs to use the same JobManager, which increases the utilization of JobManager resources. Therefore, this type of cluster is suitable for jobs that consume few resources or jobs that start and stop frequently.
**Note:** If you need to enable the SQL preview feature, you must select **Session Clusters** and turn on Use for SQL Editor previews. For more information, see [Debug a job](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Debug a job.md) and [Configure a session cluster](/intl.en-US/Fully Managed Flink/Configure a session cluster.md). |

    6.  Click **OK**.

2.  On the Draft Editor page, write data definition language \(DDL\) and data manipulation language \(DML\) statements.

    Sample statements:

    ```
    -- Create a datagen_source source table. 
    CREATE TEMPORARY TABLE datagen_source(
      name VARCHAR
    ) WITH (
      'connector' = 'datagen'
    );
    -- Create a blackhole_sink result table. 
    CREATE TEMPORARY TABLE blackhole_sink(
      name  VARCHAR
    ) with (
      'connector' = 'blackhole'
    );
    
    -- Insert data from the datagen_source source table into the blackhole_sink result table. 
    INSERT INTO blackhole_sink
    SELECT
      name
    from datagen_source;
    ```

3.  On the right side of the Draft Editor page, click **Advanced** and enter configurations.

    The following table describes the parameters.

    |Section|Parameter|Description|
    |-------|---------|-----------|
    |**Basic Configuration**|Deployment Target|You can modify the deployment target that you selected when you create the job.|
    |Parallelism|The number of parallel jobs.|
    |Additional Dependencies|If you need to add additional dependencies, select a file or enter a valid file address in the field.|
    |**Configuration**|Flink Version|You can use 1.10, 1.11, and 1.12.|
    |Flink Image Tag|The Flink image tag.|
    |Kerberos Authentication|Enter the address of a file or upload a file for ZooKeeper authentication.|
    |**Behavior**|Restore Strategy|The policy that restores the status of a job after the job is changed to the RUNNING state. Valid values:    -   Latest Savepoint: Restore the job from the latest savepoint.
    -   Latest State: Restore the job from the latest savepoint or checkpoint.
    -   None: Restore the job without the state. After the job is restored, the job is stateless and the calculation needs to be restarted. |
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
    |Flink Master Failover Configuration \(High Availability\)|Valid values:    -   None: The system does not save metadata that is required when JobManager is restarted. This is the default value.
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

4.  Click **Save**.

5.  Click **Validate**.

6.  After the verification succeeds, click **Execute**.

7.  Click **Publish**.


