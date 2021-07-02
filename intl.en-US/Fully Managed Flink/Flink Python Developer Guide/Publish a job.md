---
keyword: [publish, publish a job, Python API, Python]
---

# Publish a job

This topic describes how to publish a Python job to a fully managed Flink cluster.

## Upload resources

Before you run a Python API job, perform the following steps to upload the Python job file or Python dependency file to the console of fully managed Flink.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Artifacts**.

4.  Click **Upload Artifact**. Select the Python job file or Python dependency file that you want to upload.


## Create a job

1.  Log on to the console of fully managed Flink to create a job.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Draft Editor**.

    4.  Click **New**.

    5.  In the **New Draft** dialog box, enter the job configuration information.

        |Parameter|Description|
        |---------|-----------|
        |**Name**|The name of the job that you want to create. **Note:** The job name must be unique in the existing project. |
        |**Type**|Both streaming and batch jobs support the following file types:        -   SQL
        -   JAR
        -   PYTHON
**Note:** Ververica Runtime \(VVR\) 3.0.1 and later and Ververica Platform \(VVP\) 2.4.1 support the batch processing feature. |
        |**Deployment Target**|The cluster in which the job is deployed. **Note:** Python jobs support only **Per-Job Clusters**. |
        |**Locate**|The folder in which the code file of the job is saved. You can also click the ![Create Folder](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0161315261/p277156.png) icon next to an existing folder to create a subfolder. |

    6.  Click **OK**.

2.  On the Draft Editor page, enter the basic configuration information.

    You can directly enter the following configuration information. You can also click **YAML** in the lower-right corner of the page to directly modify the configuration information. The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Python Uri|The Uniform Resource Identifier \(URI\) to access the Python job file that you want to upload. Python job files can be .py files or .zip files.|
    |Entry Module|The entry point class of the program. If you select a .py Python job file, you do not need to specify this parameter. If you select a .zip Python job file, you must specify this parameter. For example, you can enter example.word\_count in this field.|
    |Entrypoint main args|Job parameters.|
    |Python Libraries|A third-party Python package. The third-party Python package that you uploaded is added to PYTHONPATH of the Python worker process so that the package can be directly accessed in Python custom functions. For more information about how to use third-party Python packages, see [Use a third-party Python package](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Python dependencies.md).|
    |Python Archives|Archive files. Only ZIP files such as .zip, .jar, .whl, and .egg are supported. Archive files are decompressed to the working directory of the Python worker process. For example, if the name of the compressed file where the archive files are located is mydata.zip, the following code can be written in the Python user-defined function to access the mydata.zip archive file.

    ```
def map():  
    with open("mydata.zip/mydata/data.txt") as f: 
    ...
    ```

For more information about Python Archives, see [Use a custom Python virtual environment](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Python dependencies.md) and [Use data files](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Python dependencies.md). |
    |Additional Dependencies|The JAR packages or data files that you want to upload. The selected files are automatically uploaded to the /flink/usrlib/ directory of the node where the job is running.|
    |Parallelism|The number of jobs that run in parallel.|

3.  On the right side of the Draft Editor page, click the **Advanced** tab and enter the configuration information based on your business requirements.

    The following table describes the parameters.

    |Section|Parameter|Description|
    |-------|---------|-----------|
    |**Configuration**|Flink Version|Valid values: 1.10, 1.11, and 1.12.|
    |Flink Image Tag|Select a Flink image tag.|
    |**Behavior**|Restore Strategy|The policy to restore the job status when the job is changed to the RUNNING state. Valid values:    -   Latest Savepoint: Restore the job from the latest savepoint.
    -   Latest State: Restore the job from the latest savepoint or checkpoint.
    -   None: Restore the job without the state. After the job is restored, the job is stateless and the calculation needs to be restarted. |
    |Max Job Creation Attempts|The number of retries after the instance fails to be created.|
    |Stop with Drain|If you turn on Stop with Drain, the existing data result in a window is returned when you manually terminate a job or perform a stateful upgrade for a job, regardless of whether the conditions to close the window are met.|
    |**Flink Configuration**|Checkpointing Interval|The interval at which a checkpoint is run on a scheduled basis. If you leave this parameter empty, the checkpointing feature is disabled.|
    |Min Time Between Checkpoints|The minimum interval between two checkpoints. If the maximum parallelism of checkpoints is one, this parameter specifies the minimum interval between two checkpoints.|
    |Retention Policy|Specifies whether to retain the last checkpoint if the job cannot be restarted or the job is suspended. Valid values:    -   Always: The last checkpoint is retained if the job is stopped.
    -   Only when FAILED: The last checkpoint is deleted if the job is stopped.
    -   Never: The last checkpoint is not retained. This is the default value. |
    |Enabled Unaligned Checkpoints|If you turn on Enabled Unaligned Checkpoints, the running time of a checkpoint is significantly reduced during backpressure. However, this increases the state size of a single checkpoint.|
    |Flink Restart Strategy Configuration|If a task fails and checkpointing is disabled, JobManager cannot be restarted. If checkpointing is enabled, JobManager is restarted. Valid values:    -   Failure Rate: JobManager is restarted if the number of failures within the specified interval exceeds the upper limit.
    -   Fixed Delay: JobManager is restarted at a fixed interval.
    -   No Restarts: JobManager is not restarted. This is the default value. |
    |Flink Master Failover Configuration \('High Availability'\)|Valid values:    -   None: The system does not save metadata that is required when JobManager is restarted. This is the default value.
    -   Kubernetes: The system saves metadata to a Kubernetes cluster for JobManager to restart. |
    |Additional Configuration|Other Flink settings, such as `taskmanager.numberOfTaskSlots: 1`.|
    |**Resources**|Number of Task Managers|By default, the value of this parameter is the same as the value of Parallelism.|
    |Job Manager CPUs|Default value: 1.|
    |Job Manager Memory|The minimum value is 500 MiB. We recommend that you use GiB or MiB as the unit. For example, you can set this parameter to 1024 MiB or 1.5 GiB.|
    |Task Manager CPUs|Default value: 1.|
    |Task Manager Memory|The minimum value is 1 GiB. We recommend that you use GiB or MiB as the unit. For example, you can set this parameter to 1024 MiB or 1.5 GiB.|
    |**Logging**|Root Log Level|Valid values: TRACE, DEBUG, INFO, WARN, and ERROR.|
    |Log Levels|Enter the log name and log level.|
    |Logging Profile|The log template. You can use the system template or configure a custom template.|

4.  In the upper-right corner of the Draft Editor page, click **Publish**.


