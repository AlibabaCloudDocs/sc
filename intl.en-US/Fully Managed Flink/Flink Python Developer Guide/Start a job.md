# Start a job

After you develop and publish a job, you must start the job on the Developments page to make the job enter the RUNNING state.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Deployments**.

4.  On the Deployments page, find the job that you want to start, and click **Start** in the **Actions** column.

    After you click **Start**, you can view the change process from a current state to a desired state and the final result. The following tables describe the meaning of the desired states and current states.

    -   **Desired states**

        |State|Description|
        |-----|-----------|
        |RUNNING|The job is running normally.|
        |CANCELLED|The job is stopped.|
        |SUSPENDED|The job is suspended.|

    -   **Current states**

        |State|Description|
        |-----|-----------|
        |RUNNING|The job is running normally.|
        |CANCELLED|The job is stopped.|
        |SUSPENDED|The job is suspended.|
        |FINISHED|The job is completed.|
        |TRANSITIONING|The job is transitioning to the desired state.|
        |FAILED|The job status fails to be transitioned to the desired state. Manual processing is required.|
        |ClusterUnreachable|Ververica Platform \(VVP\) fails to access JobManager \(JM\) to obtain the job status.|


