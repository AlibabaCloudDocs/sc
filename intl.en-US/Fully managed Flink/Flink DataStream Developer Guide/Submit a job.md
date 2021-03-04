---
keyword: [submit, job submission, DataStream, Table API, Python API, Python]
---

# Submit a job

This topic describes how to submit DataStream API jobs, Table API jobs, and Python API jobs to clusters for running in fully managed Flink.

## Upload a JAR package, Python job file, or Python dependency

Before you run a DataStream API job, Table API job, or Python API job, perform the following steps to upload a JAR package, Python job file, or Python dependency through the development console of fully managed Flink:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Artifacts**.

4.  Click **Upload Artifact**. Select the JAR package, Python job file, or Python dependency that you want to upload.

    If the job is a Python API job, upload the official JAR package of PyFlink. For information about how to download the official JAR package, see [PyFlink V1.11](https://repo1.maven.org/maven2/org/apache/flink/flink-python_2.11/1.11.2/flink-python_2.11-1.11.2.jar).


## Create a job

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  Click **Create Deployment**.

4.  On the **Standard** page, enter the basic configurations.

    ![Create a job](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5696604161/p132936.png)

    **Note:**

    -   You can click **Show Advanced Form** and configure **Behavior**, **Flink Configuration**, **Resources**, and **Logging** as needed.
    -   In the upper-right corner of the **Create Deployment** page, you can click **Edit with YAML** to modify the configuration information.
    The following table describes the parameters.

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |**Standard**|Deployment Name|The name of the job.|
    |Jar URI|If the job is a Python API job, upload the official JAR package of PyFlink. For information about how to download the official JAR package, see [PyFlink V1.11](https://repo1.maven.org/maven2/org/apache/flink/flink-python_2.11/1.11.2/flink-python_2.11-1.11.2.jar).|
    |Entrypoint Class|The entry point class of the program. If the job is a Python API job, set Entrypoint Class to `org.apache.flink.client.python.PythonDriver`.**Note:** If you do not specify a main class for the JAR package, enter a standard path in the **Entrypoint Class** field. |
    |Entrypoint main args|If the job is a Python API job, upload the Python job file first. By default, after you upload the Python job file, the file is located in the /flink/usrlib/ directory of the node that runs the job.If the Python job file is named word\_count.py, set Entrypoint main args to `-py /flink/usrlib/word_count.py`.

**Note:** Enter a full path of the Python job file. You cannot omit or change /flink/usrlib/. |
    |Parallelism|The number of concurrent jobs.|
    |**Show Advanced Form**|**Behavior**|Upgrade Strategy|The behavior of Application Manager when a job has been changed and needs to be upgraded. Valid values:    -   Stateless: Terminate the current job, and start a new job by using the latest configuration.
    -   Stateful: Perform a stateful upgrade by creating a savepoint for the job and starting a new job from the savepoint. The new job uses the latest configuration.
    -   None: When you change the configuration, the system does not perform an automatic restart for a running Flink job. |
    |Initial State|The initial status of the job. Valid values:    -   Running: The job is running after it is created.
    -   Cancelled: You must manually start the job after it is created. |
    |Restore Strategy|The policy to restore the job status when the job is changed to the RUNNING state. Valid values:    -   Latest Savepoint: Restore the job from the latest savepoint.
    -   Latest State: Restore the job from the latest savepoint or checkpoint.
    -   None: Restore the job to stateless.
**Note:** **Upgrade Strategy** and **Restore Strategy** must be used in pairs. If you set **Upgrade Strategy** to **Stateful**, set **Restore Strategy** to **None**. In this case, after a job is restored from a savepoint, the job is restarted from the initial start time instead of the savepoint. |
    |Max Savepoint Creation Attempts|The number of retries when a savepoint fails to be created during the upgrade.|
    |Max Job Creation Attempts|The number of retries when an instance fails to be started.|
    |Stop with Drain|Assume that you have enabled Stop With Drain. In this case, if you manually terminate a job or perform a stateful upgrade for a job, the existing data result in a window is returned, regardless of whether the conditions to close the window are met.|
    |**Configuration**|Labels|You can configure labels for jobs in the Labels section. This allows you to find a job on the Overview page in an efficient manner.|
    |Label value|N/A|
    |Additional Dependencies|If the job is a Python API job, select a Python job file and the dependencies in the Additional Dependencies field. For more information about Python dependencies, see [Manage Python dependencies](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/python/table-api-users-guide/dependency_management.html#python-dependency).**Note:**

    -   By default, after you upload a Java dependency, the dependency is located in the oss://ossBucketName/artifacts/namespaces/namespaceName/ directory of the node that runs the job.
    -   By default, after you upload a Python dependency, the dependency is located in the /flink/usrlib/ directory of the node that runs the job. |
    |Flink Version|Only two major versions Flink 1.10 and Flink 1.11 are supported.**Note:** For Python API jobs, select Flink 1.11 or later. |
    |Flink Image Tag|Select a Flink image tag.**Note:** For Python API jobs, select 1.11.2-vvr-2.1.1 or later. |
    |**Flink Configuration**|Checkpointing Interval|The interval at which a checkpoint is run at a scheduled time. If you leave this parameter empty, the checkpointing feature is disabled.|
    |Min Time Between Checkpoints|The minimum interval between two checkpoints. If the maximum parallelism of checkpoints is one, this parameter specifies the minimum interval between two checkpoints.|
    |Retention Policy|Specifies whether to retain the latest checkpoint when the job cannot be restarted or the job is suspended. Valid values:    -   Always
    -   Only when FAILED
    -   Never |
    |Enabled Unaligned Checkpoints|If you enable this feature, the running time of a checkpoint can be reduced in a significant way during backpressure. However, this also increases the state size of a single checkpoint.|
    |Flink Restart Strategy Configuration|Flink Default is the default restart strategy. In this case, if a task fails and checkpointing is disabled, JobManager cannot be restarted. If you enable checkpointing, JobManager is restarted. Valid values:    -   No Restarts
    -   Fixed Delay
    -   Failure Rate |
    |**Additional Configuration**|Configuration|Configure other Flink settings. For example, `taskmanager.numberOfTaskSlots: 1`.|
    |**Resources**|Number of Task Managers|By default, the value is the same as the parallelism.|
    |Job Manager CPUs|Default value: 1.|
    |Job Manager Memory|Minimum value: 500Mi. We recommend that you use Gi or Mi as the unit. For example, 1024Mi or 1.5Gi.|
    |Task Manager CPUs|Default value: 1.|
    |Task Manager Memory|Minimum value: 1Gi. We recommend that you use Gi or Mi as the unit. For example, 1024Mi or 1.5Gi.|
    |**Logging**|Root Log Level|Valid values: TRACE, DEBUG, INFO, WARN, and ERROR.|
    |Log Levels|Enter the log name and log level.|
    |Logging Profile|The log template. You can use the system template or configure a custom template.|

5.  Click **Create Deployment**.


## Start a job

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Deployments**.

4.  Click the name of the job that you create.

5.  On the details page of the job, click **Start** to submit the job to the cluster for running.

    ![Start a job](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5696604161/p132974.png)


