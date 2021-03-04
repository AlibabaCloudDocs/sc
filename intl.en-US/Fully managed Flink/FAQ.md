---
keyword: [Internet, access, OSSException, undefined]
---

# FAQ

This topic summarizes frequently asked questions \(FAQ\) about fully managed Flink and provides solutions.

-   [How does a fully managed Flink cluster access the Internet?](#section_8j0_s5r_5s5)
-   [How does Realtime Compute for Apache Flink access the storage resources in different VPCs?](#section_tdy_78a_t4a)
-   [How do I find the job that triggers an alert?](#section_qst_xyc_bf3)
-   [How do I view information, such as the workspace ID?](#section_mc5_acj_02s)
-   [How do I upload a JAR file in the Object Storage Service \(OSS\) console?](#section_b7y_pbf_542)
-   [How do I configure Rocksdb Statebackend for an SQL job?](#section_xl7_34s_sye)
-   [What do I do if an undefined error occurs?](#section_0n6_jx3_4nt)
-   [What do I do if an OSSException error occurs?](#section_if4_l6i_qkj)
-   [What do I do if the exceeded quota: resourcequota error occurs?](#section_kwo_lhp_iul)

## How does a fully managed Flink cluster access the Internet?

-   Background information

    By default, fully managed Flink clusters cannot access the Internet. However, NAT Gateway that is provided by Alibaba Cloud can enable communications between virtual private clouds \(VPCs\) and the Internet. This meets the requirements of some users of fully managed Flink clusters to access the Internet by using user-defined extensions \(UDXs\) or Datastream code.

-   Solution

    Create a NAT gateway in the VPC. Then, create a Source Network Address Translation \(SNAT\) entry to bind the vSwitch where the fully managed Flink cluster resides to an elastic IP address \(EIP\). This way, the cluster can access the Internet by using the EIP. For more information, see the following topics:

    -   [Create a NAT gateway](/intl.en-US/Pricing/Purchase a NAT gateway.md).
    -   [Create a SNAT entry to access the Internet](/intl.en-US/User Guide/Create a SNAT entry to access the Internet.md).
    -   [Associate an EIP with a NAT gateway](/intl.en-US/User Guide/Create NAT gateways.md).

## How does Realtime Compute for Apache Flink access the storage resources in different VPCs?

You can use the following methods to allow Realtime Compute for Apache Flink to access the storage resources in different VPCs:

-   [Submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23). Select **VPC** as the product name. **Express Connect** or other products are required for network connections. You are charged if you use this method.
-   Use VPN gateways to establish VPN connections between VPCs. For more information, see [Establish IPsec-VPN connections between two VPCs](/intl.en-US/User Guide/Configure IPsec-VPN connections/Establish IPsec-VPN connections between two VPCs.md).
-   Unsubscribe the storage service that resides in a different VPC from fully managed Flink. Purchase a storage service in the same VPC as fully managed Flink.
-   Release the fully managed Flink service and then purchase a fully managed Flink service in the same VPC as the storage service.
-   Enable fully managed Flink to access the Internet so that it can access storage services over the Internet. By default, fully managed Flink clusters cannot access the Internet. If you require that fully managed Flink clusters access the Internet, see [How does a fully managed Flink cluster access the Internet?](#section_8j0_s5r_5s5) for more information.

    **Note:** We recommend that you do not use this method because Internet connections have a longer latency than connections over VPCs.


## How do I find the job that triggers an alert?

The alert event contains JobID and DeploymentID information. However, the JobID changes after a failover occurs for the job. Therefore, you must use the Deployment ID to find the job for which the error is reported. The Deployment ID information is unavailable in the development console of fully managed Flink. You must obtain the Deployment ID information from the URL of the job.

![Deployment ID](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3073484161/p180106.png)

## How do I view information, such as the workspace ID?

On the right side of the desired workspace name in the console, choose **More** \> **Workspace Details**.

![Workspace details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3073484161/p183281.png)

## How do I upload a JAR file in the Object Storage Service \(OSS\) console?

1.  In the development console of fully managed Flink, view the OSS bucket of the current cluster.

    ![OSS Bucket](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3073484161/p186374.png)

    The following figure shows information about the OSS bucket.

    ![Bucket details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3073484161/p230660.png)

2.  Log on to the OSS console and upload the JAR file to the /artifacts/namespaces directory of the destination bucket.

    ![Directory of the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4355434161/p186522.png)

3.  In the left-side navigation pane of the development console of fully managed Flink, click **Artifacts** to view the JAR file that is uploaded through the OSS console.

    ![View the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4355434161/p186527.png)


## How do I configure Rocksdb Statebackend for an SQL job?

In the upper-right corner of the desired job details page, click **Configure**. In the **Additional Configuration** section, add the following two lines of code and save changes. The changes take effect.

```
state.backend: rocksdb 
state.backend.incremental: true
```

![Details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4355434161/p182189.png)

## What do I do if an undefined error occurs?

-   Error details

    ![Error message](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4355434161/p141940.png)

-   Cause

    Your JAR file is large.

-   Solution

    You canupload the JAR file in the [OSS console](https://partners-intl.console.aliyun.com/#/oss).


## What do I do if an OSSException error occurs?

-   Error details

    The error is reported when a new job is started or a published job is restarted.

    ![Error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5355434161/p147396.png)

-   Cause

    The versioning feature is enabled for OSS. This results in a large number of objects.

-   Solution
    1.  In the [OSS console](https://oss.console.aliyun.com/), disable the versioning feature. For more information, see [Configure versioning](/intl.en-US/Console User Guide/Manage buckets/Redundancy for fault tolerance/Configure versioning.md).
    2.  Delete all the objects in the oss://\{bucket\}/flink-jobs/namespaces/\{namespace\}/jobs/ directory.

        ```
        ./ossutil64 rm oss://{bucket}/flink-jobs/namespaces/{namespace}/jobs/ --all-versions -r
        ```

        **Note:**

        -   In this topic, [ossutil](/intl.en-US/Tools/ossutil/Overview.md) is used. You can also use other methods to delete the objects.
        -   For command details, see [rm](/intl.en-US/Tools/ossutil/Common commands/rm.md).
    3.  Restart the fully managed Flink job.

## What do I do if the exceeded quota: resourcequota error occurs?

-   Error details

    The error is reported when the job is started.

    ![Error](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5355434161/p180066.png)

-   Cause

    The job fails to be started because the resources of the current project are insufficient.

-   Solution

    You must reconfigure the project resources. For more information, see [Reconfigure resources for a single project](/intl.en-US/Fully managed Flink/Manage projects.md).


