# Configure monitoring alerts \(in the development console of fully managed Flink\)

Fully managed Flink allows you to configure monitoring alerts for job running. If a monitoring rule is triggered during job running, the system sends you an alert message. This helps you detect and handle exceptions at the earliest opportunity. This topic describes how to configure monitoring alerts in the development console of fully managed Flink.

Application Real-Time Monitoring Service \(ARMS\) is activated. For more information, see [Activate and upgrade ARMS](/intl.en-US/Quick start/Activate and upgrade ARMS.md).

In the development console of fully managed Flink, you can quickly configure monitoring alerts. The development console is easy to operate and use and delivers high efficiency. You can also configure monitoring alerts in ARMS, but you must specify information, such as the cluster, type, dashboard, and Prometheus Query Language \(PromQL\). However, this method features complex operations, requires high learning costs, and delivers low efficiency for alert configuration. For more information, see [Configure monitoring alerts \(in the ARMS console\)](/intl.en-US/Fully managed Flink/O&M management/Configure monitoring alerts (in the ARMS console).md).

## Create a monitoring alert rule

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **Deployments**.

4.  Click the name of the desired deployment.

5.  Click the **Alarm Configuration** tab.

6.  Click the **Alarm Rules** tab.

7.  Click **Add Rule**.

8.  Choose **Add Rule** \> **Custom Rule**.

    If you have configured an alert template in Administration in the development console of fully managed Flink, you can also click the name of the alert template. Then, continue to perform subsequent steps to make the configuration of monitoring alerts more efficient. For more information about how to create an alert template, see [Create an alert template](#section_ppt_9m7_u9d).

9.  Enter information about the alert rule.

    ![Rule information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7945434161/p177121.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |**Rule**|Name|The name must be 3 to 64 characters in length and can contain lowercase letters, digits, and underscores \(\_\). It must start with a letter.|
    |Description|The remarks of the rule.|
    |Content|Configure the content of the condition that triggers an alert. After the condition is configured, the system compares the value of the specified metric with the threshold at a specified interval. If the result meets the condition, the alert is automatically generated.In the condition:

    -   Metric: Two types of metrics are supported.
        -   Restart Count in 1 Minute: the number of times that Job Manager is restarted in 1 minute
        -   Checkpoint Count in 5 Minutes: the number of times that the checkpoint succeeds in 5 minutes
    -   Time interval N: A check is performed once every N minutes. The time interval must be equal to or less than 60 min. Unit: min.
    -   Comparator: The \>= and <= comparators are supported.
    -   Threshold: The value that is used to compare with the metric. |
    |Effective Time|The time when alert monitoring takes effect. You can specify that alert monitoring takes effect during the day \(from 09:00 to 18:00\). By default, alert monitoring takes effect throughout the day.|
    |Alarm Rate|Valid values: 1 Min and 1 Day. This indicates that an alert is sent once per minute or per day.|
    |**Notification**|Notify Way|The following three notification delivery methods are supported:    -   DingDing
    -   Email
    -   SMS
**Note:** You can configure the phone number, mailbox, and DingTalk information of a contact in contacts. |
    |Contact Group|The contacts or contact groups that can be notified. You can click the buttons below this field to edit contacts and contact groups.|

10. Click **Save**.

    By default, the saved alert rule is enabled and appears in the alert rule list. You can stop, edit, or delete the alert rule.


## Create an alert template

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **Alarm Templates**.

4.  Click **Add Alarm Template**.

5.  Enter information about the rule.

    ![Alert templates](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7945434161/p177127.png)

    |Category|Parameter|Description|
    |--------|---------|-----------|
    |**Rule**|Name|The name must be 3 to 64 characters in length and can contain lowercase letters, digits, and underscores \(\_\). It must start with a letter.|
    |Description|The remarks of the rule.|
    |Content|Configure the content of the condition that triggers an alert. After the condition is configured, the system compares the value of the specified metric with the threshold at a specified interval. If the result meets the condition, the alert is automatically generated.In the condition:

    -   Metric: Two types of metrics are supported.
        -   Restart Count in 1 Minute: the number of times that Job Manager is restarted in 1 minute
        -   Checkpoint Count in 5 Minutes: the number of times that the checkpoint succeeds in 5 minutes
    -   Time interval N: A check is performed once every N minutes. The time interval must be equal to or less than 60 min. Unit: min.
    -   Comparator: The \>= and <= comparators are supported.
    -   Threshold: The value that is used to compare with the metric. |
    |Effective Time|The time when alert monitoring takes effect. You can specify that alert monitoring takes effect during the day \(from 09:00 to 18:00\). By default, alert monitoring takes effect throughout the day.|
    |Alarm Rate|Valid values: 1 Min and 1 Day. This indicates that an alert is sent once per minute or per day.|
    |**Notification**|Notify Way|The following three notification delivery methods are supported:    -   DingDing
    -   Email
    -   SMS
**Note:** You can configure the phone number, mailbox, and DingTalk information of a contact in contacts. |
    |Contact Group|The contacts or contact groups that can be notified. You can click the buttons below this field to edit contacts and contact groups.|

6.  Click **Save**.

    The saved alert rule appears in the alert template list. You can edit or delete the alert rule.


