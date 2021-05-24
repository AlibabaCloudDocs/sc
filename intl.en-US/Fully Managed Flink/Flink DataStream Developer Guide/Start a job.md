# Start a job

After you develop and publish a job, you must start the job on the Developments page to make the job running.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Deployments**.

4.  Find the job that you want to start, and click **Start** in the **Actions** column.

    After you click **Start**, you can view the transition process from a current state to a desired status and the final result. The following tables describe the meaning of the desired states and current states.

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
        |TRANSITIONING|The job is in the phase of transitioning to a desired state.|
        |FAILED|The job status fails to be transitioned. Manual processing is required.|
        |ClusterUnreachable|Ververica Platform \(VVP\) fails to access JobManager \(JM\) to obtain the job status.|


