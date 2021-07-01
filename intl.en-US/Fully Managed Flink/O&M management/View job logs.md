---
keyword: [view logs, job logs]
---

# View job logs

This topic describes how to view job logs on the Flink UI, Object Storage Service \(OSS\), or Log Service.

## View job logs on the Flink UI

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

4.  Click the name of the job whose logs you want to view.

    **Note:** Select the job whose **desired state** and **current state** are both **RUNNING**.

5.  Click **Flink UI**.

6.  In the left-side navigation pane, click **Task Managers** or **Job Manager**.

    -   After you click **Task Managers**, you can find the job and click the information in the **Path, ID** column to view Task Manager logs of the job.
    -   After you click **Job Manager**, you can view Job Manager logs of the job on the **Logs** tab.

## View job logs on OSS

Before you view job logs on OSS, you must have specified that job logs are exported to OSS. For more information, see [Configure job logs](/intl.en-US/Fully Managed Flink/O&M management/Configure job logs.md).

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

2.  In the left-side navigation pane, click **Buckets**.

3.  Find the bucket that you configure when you activate **fully managed Flink**, and click the name of the bucket in the Bucket Name column.

4.  Click **Files**.

5.  View the log information in the related directory.

    ![Log directories](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0945434161/p131712.png)

    |Folder|Description|
    |------|-----------|
    |artifacts|The uploaded JAR file is stored in this directory.|
    |flink-jobs|The checkpoints of Flink jobs are stored in this directory.|
    |flink-savepoints|If you click **Savepoint** in the upper-right corner of the console of **fully managed Flink**, the **Savepoint** operation is triggered and the final **Savepoint** file is stored in this directory.|
    |logs|If you set **Logging Profile** to **OSS** for your job, the logs of your job are stored in this directory. **Note:** In the logs directory, jobs are stored in the following path: logs/$\{Workspace ID\}/$\{Job ID\}/$\{Instance ID\}. You can find the Job Manager and Task Manager logs of the related job by using this directory structure. |


## View job logs on Log Service

Before you view job logs on Log Service, you must have specified that job logs are exported to Log Service. For more information, see [Configure job logs](/intl.en-US/Fully Managed Flink/O&M management/Configure job logs.md).

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  On the **Projects** tab, click the project whose logs you want to view.

3.  On the **Logstores** tab, click the desired Logstore.

4.  Enter a query and analysis statement, specify a time range, and then click **Search & Analyze**.

    For more information, see [Query logs](/intl.en-US/Index and query/Query logs.md).


