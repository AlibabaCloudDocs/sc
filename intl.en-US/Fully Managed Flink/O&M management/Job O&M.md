---
keyword: [O&M, view the job status, modify job configurations]
---

# Job O&M

This topic describes how to view the details and status of jobs, change the job status, and modify job configurations.

## View job details

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

4.  Click the name of the job whose detailed information you want to view.

5.  On the job details page, view the information shown in the following figures.

    -   **Overview**: displays the overview of job parameters.

        ![Overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7256766261/p247432.png)

        **Note:** You can click the **Metrics** tab to view more metrics, including common metrics of Flink.

    -   **Metrics**: displays common metrics of Flink.

        ![Metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8256766261/p247433.png)

        The following table lists the metrics that are supported by fully managed Flink powered by Ververica Runtime \(VVR\) 2.1.3 and later.

        **Note:** The check \(√\) sign indicates that metrics can be reported. The cross \(x\) sign indicates that metrics cannot be reported.

        -   Source

            |Connector|numBytesIn|numBytesInPerSecond|numRecordsIn|numRecordsInPerSecond|numRecordsInErrors|currentFetchEventTimeLag|currentEmitEventTimeLag|watermarkLag|sourceIdleTime|pendingBytes|pendingRecords|
            |---------|----------|-------------------|------------|---------------------|------------------|------------------------|-----------------------|------------|--------------|------------|--------------|
            |Kafka|√|√|√|√|x|√|√|√|√|x|✓|
            |MaxCompute|√|√|√|√|x|x|x|x|x|x|x|
            |Incremental MaxCompute|√|√|√|√|x|x|x|x|x|x|x|
            |Message Queue|√|√|√|√|√|√|√|√|√|x|x|
            |Log Service|x|x|√|√|x|√|√|√|x|x|x|
            |DataHub|x|x|√|√|x|√|√|√|x|x|x|
            |Elasticsearch|x|x|√|√|x|x|x|x|x|x|✓|
            |Hologres|x|x|√|√|x|x|x|x|x|x|x|
            |ApsaraDB for HBase|x|x|x|x|x|x|x|x|x|x|x|
            |Tablestore|x|x|x|x|x|x|x|x|x|x|x|
            |Phoenix|x|x|x|x|x|x|x|x|x|x|x|
            |ApsareDB for Redis|x|x|x|x|x|x|x|x|x|x|x|

        -   Sink

            |Connector|numBytesOut|numBytesOutPerSecond|numRecordsOut|numRecordsOutPerSecond|numRecordsOutErrors|currentSendTime|
            |---------|-----------|--------------------|-------------|----------------------|-------------------|---------------|
            |Kafka|√|√|√|√|√|√|
            |MaxCompute|x|x|x|✓|x|✓|
            |Incremental MaxCompute|x|x|x|✓|x|✓|
            |Message Queue|√|√|x|x|x|✓|
            |Log Service|x|x|√|√|x|✓|
            |DataHub|√|√|√|√|x|✓|
            |Elasticsearch|x|x|x|x|x|x|
            |Hologres|x|x|√|√|x|✓|
            |ApsaraDB for HBase|√|√|√|√|x|✓|
            |Tablestore|x|x|√|√|x|✓|
            |Phoenix|x|x|√|√|x|✓|
            |ApsaraDB for Redis|x|x|√|√|x|✓|

        **Note:** You can view metrics of fully managed Flink in the console of fully managed Flink or by using the self-managed Prometheus service. However, when the network is connected, you must add the following code in the **Additional Configuration** section for the job in the console of fully managed Flink:

        ```
        metrics.reporter.promgatewayappmgr.class: org.apache.flink.metrics.prometheus.PrometheusPushGatewayReporter
        metrics.reporter.promgatewayappmgr.host: ${your pushgateway host}
        metrics.reporter.promgatewayappmgr.port: ${your pushgateway port}
        ```

        In the code, you must replace $\{your pushgateway host\} with the hostname of your Pushgateway and replace $\{your pushgateway port\} with the port number of your Pushgateway.

        In addition, you can obtain the metrics of fully managed Flink by calling Application Real-Time Monitoring Service \(ARMS\) API operations and integrate the metrics to your platform. For more information about ARMS API operations, see [List of API operations by feature](/intl.en-US/API Reference/List of API operations by feature.md). For more information about operator-related metrics, see [Operator Metric](https://cwiki.apache.org/confluence/display/FLINK/FLIP-33%3A+Standardize+Connector+Metrics).

    -   **Events**: displays the events that occur when the job is running.

        ![Events](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8256766261/p247434.png)

        On the **Events** tab, the following information about events is displayed by default: **Time**, **Message**, and **Actions**. Actions include:

        -   **Copy Message**: Copy message content.
        -   **Job Detail**: View the details about the events that are generated by the job, including Deployment, Artifact Configuration, Flink Configuration, Logging Configuration, and Logging Level.

            **Note:**

            -   In the upper-right corner of the **Overview** tab, you can click **Flink UI** to go to the open source **Flink UI** of the running job.
            -   You can click **Metrics** to go to the metric page of the Ververica Platform \(VVP\) of the running job. **By default, the metric page displays information about the metrics that are generated within 30 minutes after the event occurs.**
        -   **Filter Events for this Job**: Find the events of this job.

## View the job status

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

4.  Click the name of the job whose status you want to view.

5.  On the **Overview** tab, view the status of the job.

    In the upper-left corner of the tab, two types of the job status appear:

    -   **Desired states**

        |State|Description|
        |-----|-----------|
        |RUNNING|The job is started based on the job configurations.|
        |CANCELLED|A running job is terminated.|
        |SUSPENDED|A savepoint is created for the running job before the job is terminated.|

    -   **Current states**

        |State|Description|
        |-----|-----------|
        |RUNNING|The job is started based on the job configurations.|
        |CANCELLED|A running job is terminated.|
        |SUSPENDED|A savepoint is created for the running job before the job is terminated.|
        |FINISHED|The job is completed.|
        |TRANSITIONING|The job is in the phase of transitioning to a desired state.|
        |FAILED|The job status fails to be transitioned to the desired state. Manual processing is required.|
        |ClusterUnreachable|VVP fails to access JobManager to obtain the job status.|

    **Note:** After you click **Start** in the Actions column, the desired state of the job becomes **RUNNING** immediately. The current state becomes **TRANSITIONING**. The startup progress of the job appears. The frontend development platform VVP ensures that Flink jobs are eventually in the desired state.

    If the status of a Flink job is transitioning to the desired state but the eventual desired state of the job changes. The job status first reaches the initial desired state and then is transitioned to the eventual desired state.


## Change the job status

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

4.  Click the name of the job whose status you want to change.

5.  Click the **Overview** tab. The job details page appears.

6.  Click the following buttons as needed to change the job status.

    ![Job status](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2776766261/p247439.png)

    The following table describes the meaning of each button.

    |Operation|Description|
    |---------|-----------|
    |Suspend|Suspend a job. This indicates that a savepoint is created for the running job before the job is terminated. To suspend a job, make sure that the following conditions are met:    -   The address of the savepoint is configured. If the address of the savepoint is not configured, a job is directly terminated when you suspend the job. This results in the loss of job status.
    -   The savepoint operation is successful. |
    |Cancel|Terminate a job.|
    |Diagnosis|The system provides you some simple guidance based on the collected instantaneous job information, such as a failover, and general job diagnostics rules.|
    |Savepoint|Create a savepoint at the current moment. **Note:** Configure the address of the savepoint first. Otherwise, the savepoint fails. |
    |Configure|Modify the basic or advanced configurations of a job.|
    |Delete|Delete a job. Only the jobs whose current state is not **RUNNING** can be deleted.|


## Modify job configurations

The job configurations include all the configuration content in the phase of publishing a job.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Applications** \> **Deployments**.

4.  Click the name of the job whose configurations you want to modify.

5.  Modify the job configurations.

    You can use one of the following methods to modify the job configurations:

    -   **Edit job configurations by using a template**

        In the upper-right corner of the page, click **Configure**. On the page that appears, click **Advanced** to modify the job configurations.

    -   **Edit job configurations by using YAML**

        In the upper-right corner of the page, click **Configure**. On the page that appears, click **YAML** to modify the job configurations.

6.  Click **Save**.


