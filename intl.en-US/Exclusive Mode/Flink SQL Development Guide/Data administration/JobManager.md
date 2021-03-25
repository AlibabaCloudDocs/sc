# JobManager

JobManager plays an important part in the startup process of a Realtime Compute for Apache Flink cluster. You can view the JobManager parameter information on the JobManager tab.

## Go to the JobManager tab

1.  Go to the **Job Administration** page.
    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
    2.  In the top navigation bar, click **Administration**.
    3.  On the **Jobs** page that appears, click the target job name under the **Job Name** field.
2.  On the **Job Administration** page, click the **JobManager** tab.

## Role of JobManager in the cluster startup process

JobManager plays an important part in the startup process of a Realtime Compute for Apache Flink cluster. The following items describe the startup process of a Realtime Compute for Apache Flink cluster:

1.  When a Realtime Compute for Apache Flink cluster is started, one JobManager and several TaskExecutors are started at the same time.
2.  The client submits tasks to the JobManager.
3.  The JobManager assigns tasks to TaskExecutors.
4.  The TaskExecutors report the heartbeat and statistical information to the JobManager.

## JobManager parameters

On the Job Administration page, click the **JobManager** tab. On the **Attempt List** tab, click **View Details** in the **Actions** column to view detailed information about the JobManager.

![JobManager](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5321659951/p34011.png)

