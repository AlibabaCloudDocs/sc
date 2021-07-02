---
keyword: [activation, procedure for activating the service]
---

# Procedure for activating the service

This topic describes how to use an Alibaba Cloud account to activate fully managed Flink and what you need to pay attention to when you activate the service.

-   You have an Alibaba Cloud account. If you do not have an Alibaba Cloud account, create one first.
-   The **fully managed Flink** service uses an architecture in which computing is decoupled from storage. This service needs to use Object Storage Service \(OSS\) to store information, such as the uploaded Java Archive \(JAR\) files, the logs of Flink jobs, and the savepoints or checkpoints that are generated in Flink jobs. If you have not activated OSS, activate it first. For more information, see [Activate OSS](/intl.en-US/Console User Guide/Sign up for OSS.md).
-   Your Alibaba Cloud account balance is not less than 100 dollars or the equivalent voucher or coupon before you activate the pay-as-you-go service.

**Note:** Server Load Balancer \(SLB\) and Application Real-Time Monitoring Service \(ARMS\) are automatically activated when you activate fully managed Flink. In addition, you must activate Virtual Private Cloud \(VPC\) and OSS. For more information about the billing standards of the involved cloud services, see [Pricing](/intl.en-US/Fully Managed Flink/Pricing.md).

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  Click **Purchase** under **Fully Managed Flink**.

3.  On the **Authorization Request** page, click **Authorize in RAM**.

    ![Authorize](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8098574161/p110414.png)

4.  On the **Cloud Resource Access Authorization** page, click **Confirm Authorization Policy** at the bottom of the page.

    ![AliyunStreamAsiDefaultRole](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8098574161/p110415.png)

    **Note:** By default, the AliyunStreamAsiDefaultRole role is selected.

5.  On the buy page, enter the configuration information.

    |Section|Parameter|Description|
    |-------|---------|-----------|
    |Basic Configurations|Billing Method|The **Subscription** and **Pay-as-you-go** billing methods are supported.|
    |Duration|You need to specify this parameter only when the **Subscription** billing method is used.|
    |Auto-renewal|You need to specify this parameter only when the **Subscription** billing method is used. After the auto-renewal feature is **enabled**, you can modify the renewal policy in **Renewal Management** in the [Alibaba Cloud Management Console](https://home.console.aliyun.com) in the future.|
    |Region|The **Singapore \(Singapore\)** region is supported. **Note:** We recommend that you select the same **region** as the upstream and downstream storage. |
    |Zone|**Note:** We recommend that you select the same **zone** as the upstream and downstream storage. |
    |Network|VPC|**Note:** We recommend that you select the same **VPC** as the upstream and downstream storage. |
    |vSwitch|An IP address is assigned to each TaskManager instance and each JobManager instance of a Flink job. You can select one to five vSwitches to properly plan the CIDR blocks based on the scale of Flink jobs.|
    |Workspace|Workspace Name|The name must be 1 to 60 characters in length and can contain letters, digits, and hyphens \(-\). It must start with a letter. **Note:**

    -   Workspace names within the same Alibaba Cloud account are unique. Do not enter a duplicate name. Otherwise, the system prompts you to enter a name again.
    -   After the **fully managed Flink** service is activated, **Workspace Name** cannot be changed. |
    |Resource Quota|You need to specify this parameter only when the **Subscription** billing method is used. Computing resources are billed based on this quota.|
    |Storage|OSS Bucket|The **fully managed Flink** service creates four directories in the bucket that you select to store different types of data:    -   artifacts: The uploaded JAR files are stored in this directory.
    -   flink-jobs: The checkpoints of Flink jobs are stored in this directory.
    -   flink-savepoints: If you click **Savepoint** in the console of **fully managed Flink**, the **Savepoint** operation is triggered and the final **Savepoint** file is stored in this directory.
    -   logs: If you set **Log Template** to **OSS** for your job, the logs of your job are stored in this directory.
**Note:**

    -   After the fully managed Flink service is activated, **OSS Bucket** cannot be changed.
    -   The OSS bucket must be in the same region as the **fully managed Flink** service. |

6.  Click **Confirm Order** and pay for the order. Fully managed Flink is activated.

    **Note:**

    -   After you complete the payment, click **Console**. Then, on the **Fully Managed Flink** tab, you can view the **workspace** that is being created. In normal cases, the **workspace** can be created in 5 to 10 minutes after you complete the payment.
    -   After fully managed Flink is activated, you can log on to the Realtime Compute for Apache Flink console to view the console interface. For more information, see [Introduction to the development console of fully managed Flink](). In addition, a project named Workspace name-default is generated by default after the workspace is created. You can develop jobs after you enter the project. If you want to create other projects, follow the instructions provided in [Manage projects](/intl.en-US/Fully Managed Flink/Manage projects.md).

