---
keyword: [view logs, job logs]
---

# View job logs

This topic describes how to view job logs on the Flink UI, Object Storage Service \(OSS\), or Log Service.

## View job logs on the Flink UI

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **Deployments**.

4.  Click the name of the deployment that you create.

    **Note:** Select a job whose **expected status** and **current status** are **RUNNING**.

5.  Click **Flink UI**.

6.  In the left-side navigation pane, click **Job Manager**.

7.  On the **Logs** tab, view the operational logs of the job.


## View job logs on OSS

Before you view job logs on OSS, you must have specified that job logs are exported to OSS. For more information, see [Configure job logs](/intl.en-US/Fully managed Flink/O&M management/Configure job logs.md).

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

2.  In the left-side navigation pane, click **Buckets**.

3.  Click the name of the bucket that you configure when you activate the **Fully Managed Flink** service.

4.  Click **Files**.

5.  In the corresponding directory, view log information.

    ![Log directory](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0945434161/p131712.png)

    |Folder|Description|
    |------|-----------|
    |artifacts|The uploaded JAR file is stored in this directory.|
    |flink-jobs|The checkpoints of Flink jobs are stored in this directory.|
    |flink-savepoints|If you click **Savepoint** in the development console of **Fully Managed Flink**, the **Savepoint** operation is triggered and the final **Savepoint** file is stored in this directory.|
    |logs|If you set **Logging Profile** to **OSS** for your job, the logs of your job are stored in this directory. **Note:** In the logs directory, jobs are stored in the following path: logs/$\{Workspace ID\}/$\{Job ID\}/$\{Instance ID\}. You can find the Job Manager \(JM\) and Task Manager \(TM\) logs of the corresponding job by using this directory structure. |


## View job logs on Log Service

Before you view job logs on Log Service, you must have specified that job logs are exported to Log Service. For more information, see [Configure job logs](/intl.en-US/Fully managed Flink/O&M management/Configure job logs.md).

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the desired project.

3.  On the **Logstores** tab, click the desired Logstore.

4.  Enter a query and analysis statement, specify a time range, and then click **Search & Analyze**.

    For more information, see [Query logs](/intl.en-US/Index and query/Query logs.md).


