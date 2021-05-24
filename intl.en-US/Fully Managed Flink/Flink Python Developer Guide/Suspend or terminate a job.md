---
keyword: [resource configuration, suspend, suspend a job, change the SQL logic, change a job version, add a parameter to the WITH clause, add a parameter for a job, terminate]
---

# Suspend or terminate a job

In the development console of fully managed Flink, you can suspend or terminate a job in the RUNNING state. This topic describes how to suspend and terminate a job in the RUNNING state.

-   Suspend a job: This operation does not clear the job status. For example, if the job that you suspend is running a COUNT operation, the COUNT operation continues from the last successful checkpoint after you resume the job. If you change the resource configuration, the configuration takes effect after the job is suspended and then resumed.
-   Terminate a job: This operation clears the job status. For example, if the job that you terminate is running a COUNT operation, the COUNT operation starts from 0 after you terminate the job and then restart it. If you have modified the SQL logic, changed the job version, added or deleted parameters to or from the WITH clause, or added parameters for a job, the changes take effect after you terminate the job and restart it.

## Suspend a job

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Deployments**.

4.  On the Deployments page, find the job that you want to suspend, and click **Suspend** in the **Actions** column.


## Terminate a job

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Deployments**.

4.  On the Deployments page, find the job that you want to terminate, and click **Cancel** in the **Actions** column.


