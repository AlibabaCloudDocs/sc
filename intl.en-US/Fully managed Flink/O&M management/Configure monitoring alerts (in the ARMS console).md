---
keyword: [Prometheus monitoring alerts, monitoring alerts, alerts, monitoring]
---

# Configure monitoring alerts \(in the ARMS console\)

Fully managed Flink allows you to configure monitoring alerts for job running. If a monitoring rule is triggered during job running, the system sends you an alert message. This helps you detect and handle exceptions at the earliest opportunity. This topic describes how to configure Prometheus monitoring alerts in the Application Real-Time Monitoring Service \(ARMS\) console.

ARMS is activated. For more information, see [Activate and upgrade ARMS](/intl.en-US/Quick start/Activate and upgrade ARMS.md).

When you configure monitoring alerts in ARMS, you must specify information, such as the cluster, type, dashboard, and Prometheus Query Language \(PromQL\). However, this method features complex operations, requires high learning costs, and delivers low efficiency for alert configuration. In the development console of fully managed Flink, you can quickly configure monitoring alerts. The development console is easy to operate and use and delivers high efficiency. For more information, see [Configure monitoring alerts \(in the development console of fully managed Flink\)]().

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **Deployments**.

4.  Click the name of the desired deployment.

    **Note:** Select a deployment whose **expected status** and **current status** are **RUNNING**.

5.  In the upper-right corner of the **Overview** page, click **Metrics**.

    ![Metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7610954161/p111848.png)

6.  In the left-side navigation pane, click **Alerting**.

    ![Alerting](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7610954161/p111850.png)

7.  In the left-side navigation pane, choose **Alerts** \> **Alert Policies**.

8.  In the upper-right corner of the page, choose **Create Alarm** \> **Prometheus**.

    ![Prometheus](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7610954161/p110436.png)

9.  In the Create Alarm dialog box, specify the following parameters.

    ![Relevant information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7610954161/p110437.png)

    |Configuration item|Description|
    |------------------|-----------|
    |**Alarm Name**|Example: alert for network receiving pressure.|
    |**Cluster**|Select the **cluster** for which you want to create the alert.|
    |**Type**|Select **grafana**.|
    |**Dashboard**|Select **Flink**.|
    |**Chart**|Select a **chart** based on your business requirements.|
    |**Alarm Rules**|Select **Meet All of the Following Criteria**.|
    |**Last N Minutes**|For example, an alert is triggered if the value of N is 5 and the average value of network receive bytes \(MB\) is greater than or equal to 3.**Note:** A Grafana chart may contain data of multiple curves, such as A, B, and C. You can specify one of the curves to monitor as needed. |
    |**PromQL**|Edit the **PromQL** statement or enter the PromQL statement again.**Note:** If the PromQL statement contains a dollar sign \(`$`\), an error occurs. You must delete the parameters on both sides of the equal sign \(`=`\) in the statement that contains the dollar sign \(`$`\). For example, change `sum (rate (container_network_receive_bytes_total{instance=~"^$HostIp.*"}[1m]))` to `sum (rate (container_network_receive_bytes_total[1m]))`. |
    |**Notification Mode**|Fully managed Flink supports three notification delivery method: text message, email, and DingTalk chatbot.|
    |**Notification Receiver**|In the **Contact Groups** section, click the name of a contact group. If the contact group appears in the **Selected Groups** section, the setting succeeds.|

10. Click **Save**.


