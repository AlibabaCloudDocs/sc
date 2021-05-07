# Activate Realtime Compute for Apache Flink and create a project

This topic describes how to activate Realtime Compute for Apache Flink in exclusive mode and how to create Realtime Compute for Apache Flink clusters and projects in exclusive mode.

## Activate Realtime Compute for Apache Flink in exclusive mode

**Note:** A Realtime Compute for Apache Flink cluster in exclusive mode can access only storage resources in the same virtual private cloud \(VPC\), region, and security group as the cluster. To allow the cluster to access resources in another VPC, use [Express Connect](/intl.en-US/Product Introduction/What is Express Connect?.md) to access the network.

After you place an order for Realtime Compute for Apache Flink in exclusive mode, you must create a cluster before you create a project.

1.  Activate Realtime Compute for Apache Flink.
    1.  Log on to the [product page of Realtime Compute for Apache Flink](https://www.alibabacloud.com/products/realtime-compute?spm=a3c0i.7911826.1097638.dnavproductsh13.1cd514b3Yx8AyU).

        **Note:** Use your Alibaba Cloud account instead of a Resource Access Management \(RAM\) user to activate Realtime Compute for Apache Flink and create a project. If you do not have an Alibaba Cloud account, create one.

    2.  Click **Console**.
    3.  Click **Buy Now** under **Blink Exclusive Cluster** to go to the buy page of Realtime Compute for Apache Flink.
    4.  Click **Realtime Compute Exclusive Mode \(Subscription\)** or **Realtime Compute Exclusive Mode \(Pay-As-You-Go\)**.
    5.  Configure the parameters, such as the region, cluster configuration, and billing duration.
    6.  Click **Buy Now**.
    7.  Read and select **I have read and agree to Realtime Compute Exclusive Mode \(Subscription\) Agreement of Service**.
    8.  Click **Pay**.
2.  Create a Realtime Compute for Apache Flink cluster.
    -   Preparations
        -   After you activate Realtime Compute for Apache Flink in exclusive mode, Realtime Compute for Apache Flink creates a security group in your VPC and applies for an elastic network interface \(ENI\). For more information, see [ENI overview](/intl.en-US/Network/Elastic Network Interfaces/ENI overview.md).

            **Note:** Do not delete this security group or ENI. Otherwise, the cluster cannot be created.

            -   If you have VPCs, specify a VPC for Realtime Compute for Apache Flink.
            -   If you do not have a VPC, activate the Alibaba Cloud VPC service first. For more information about how to activate the Alibaba Cloud VPC service, see [Plan and design a VPC](/intl.en-US/Quick Start/Plan and design a VPC.md).

                **Note:** When you create a VPC, make sure that:

                -   Sufficient Elastic Compute Service \(ECS\) instances are available in the VPC. If ECS instances are insufficient, [submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).
                -   The number of available IP addresses in a vSwitch is greater than or equal to the number of nodes in a Realtime Compute for Apache Flink cluster. For more information, see [View the nodes of a Realtime Compute for Apache Flink cluster](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md).
        -   You can upload UDF packages to a Realtime Compute for Apache Flink cluster in exclusive mode. To ensure data security, Realtime Compute for Apache Flink stores the UDF packages to an Object Storage Service \(OSS\) bucket. You must specify the OSS bucket. If you do not have an OSS bucket, create one first. For more information about how to create an OSS bucket, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).
        -   [Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.md)
    -   Procedure
        1.  After you complete the payment, click **Console**.
        2.  On the **Clusters** page, click **Create Cluster**.

            **Note:** If you have not created a project for an order, a red number is displayed in the upper-right corner of **Create Project** on the **Project Management** \> **Projects** page. The number indicates the number of orders for which you have not created a project.

        3.  In the **Select Order** step, select an order in **Order ID** and click Next.
        4.  In the **Basic Information** step, configure **Cluster Name** and **Cluster Description** and click Next.
        5.  In the **Cluster Settings** step, enter configuration information and click Next.

            **Note:** The Realtime Compute for Apache Flink cluster must reside in the same security group, region, and VPC as both the upstream and downstream data stores you purchased.

            -   **OSS Bucket**

                Select an OSS bucket to store your UDF packages. If you do not have OSS buckets, create one first. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md). When you create an OSS bucket, you must specify **Standard** for **Storage Class**. We recommend that you specify **Private** for **Access Control List \(ACL\)**. Do not select **Public Read**.

                ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4764076061/p33098.png)

            -   **VPC**

                Select the VPC that you want to access and customize the name of the VPC.

                **Note:** Realtime Compute for Apache Flink can identify only custom VPC names.

            -   **Zone**

                After you properly configure the VPC, the system automatically displays the available zones.

                **Note:** No zone or vSwitch is available in the following scenarios:

                -   ECS instances in the selected zone are insufficient. For more information about how to add an ECS instance, see [Create an instance by using the wizard](/intl.en-US/Instance/Create an instance/Create an instance by using the wizard.md).
                -   The number of available IP addresses in the selected vSwitch is less than the number of nodes in a Realtime Compute for Apache Flink cluster. For more information, see [View the nodes of a Realtime Compute for Apache Flink cluster](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md).
        6.  In the **Confirm** step, click **Create**.

            **Note:** When you create a cluster, it takes about half an hour for the cluster status to change from **Starting** to **Running**. If the cluster status does not change after a long period of time, [submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).

3.  Create a project.
    1.  In the left-side navigation pane, choose **Cluster Management** \> **Clusters**. Find the cluster for which you want to create a project and click **Create Project** in the **Actions** column.
    2.  In the **Create Project** dialog box, configure **Project Name** and **Project Description**, and slide the pointer on the right side of **Specified CUs** to specify the required number of CUs.
    3.  Click **OK**.

