# Overview

The original product line \(Blink-based Realtime Compute\) of Alibaba Cloud Realtime Compute for Apache Flink supports two service modes: exclusive mode and shared mode. The exclusive mode provides more features than the shared mode. This topic describes the benefits and differences of the two modes.

## Shared mode \(phased-out\)

In shared mode, users share physical resources such as networks, disks, CPUs, and memory in a computing cluster. Account management and control groups \(cgroups\) are used to implement resource isolation and security management. For account, business, and data security concerns, the shared mode does not support user-defined functions \(UDFs\).

**Note:** As of December 24, 2019, Realtime Compute for Apache Flink in shared mode is no longer available. You cannot purchase projects in this mode. You can only scale out, scale in, or renew existing shared-mode projects. We recommend that you purchase the exclusive mode or semi-managed Flink mode of Realtime Compute for Apache Flink based on your business requirements.

## Exclusive mode

-   Benefits

    In exclusive mode, an independent computing cluster is created on an Alibaba Cloud Elastic Compute Service \(ECS\) instance. A single user can exclusively use physical resources such as networks, disks, CPUs, and memory in the computing cluster. The resources of the user are isolated from those of other users. Compared with the shared mode, the exclusive mode provides the following benefits:

    -   Adaption to various hardware

        Leverages the capabilities of Alibaba Cloud in hardware-specific optimization such as CPU-to-memory ratio and GPU or FPGA. This solves hardware adaptation issues.

    -   Isolation between users

        Allows you to use a VPC and exclusive computing resources. In addition, you can connect your development platform to the VPC to meet your business requirements.

    -   Support for UDFs

        Isolates your network and physical machines from those of other users. This way, you can use UDFs and underlying APIs to meet your business requirements. For more information about UDFs, see [Overview](/intl.en-US/Exclusive Mode/Flink SQL/UDX/Overview.md).

    -   Rich features
        -   Extract, transform, load \(ETL\) in a data lake: You can use Flink SQL and UDFs to develop ETL tasks.
        -   Computing of heterogeneous data sources: Data can be read from heterogeneous data sources for analysis. For example, Realtime Compute for Apache Flink can read archived logs from Object Storage Service \(OSS\) buckets and associate the logs with high-risk IP addresses in an ApsaraDB for HBase database to analyze web attacks.
        -   Multiple upstream and downstream data stores are supported, such as [Create a Message Queue for Apache Kafka source table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Create a Message Queue for Apache Kafka source table.md) and [Create a Message Queue for Apache Kafka result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create a Message Queue for Apache Kafka result table.md).
-   Architecture of Realtime Compute for Apache Flink in exclusive mode

    ![Architecture of Realtime Compute for Apache Flink in exclusive mode](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4544749951/p33597.png)

    -   For Realtime Compute for Apache Flink in exclusive mode, all your purchased ECS instances are hosted in the VPC of your Realtime Compute for Apache Flink cluster. In this mode, you cannot log on to these ECS instances. If you want to log on to these ECS instances,[submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).
    -   When you create a cluster, you can apply for an elastic network interface \(ENI\) within your account to access all resources in the VPC to which the ENI belongs.
    -   To access the Internet, you can bind a NAT gateway and an elastic IP address \(EIP\) to the ENI. For more information, see [Associate an EIP with a NAT gateway](/intl.en-US/User Guide/Create NAT gateways.md).

        **Note:** You are charged for the use of the ENI only when your Realtime Compute for Apache Flink cluster accesses the Internet.

    -   To access services of other security groups in the VPC, you must configure inbound and outbound rules for the security group.

## Differences between exclusive mode and shared mode

|Item|Exclusive mode|Shared mode|
|----|--------------|-----------|
|Development of custom features|Supports UDFs and APIs. This allows you to flexibly develop jobs.|Not supported|
|Network type|Supports Alibaba Cloud VPCs. **Note:** Users in a Realtime Compute for Apache Flink cluster in exclusive mode can access only the upstream and downstream storage resources in the same region and VPC as the cluster. If you want to access resources in another VPC, you must configure port numbers of security groups and use Express Connect to access the VPC.

|Not supported|
|Machine type selection|Allows you to select a machine type when you purchase Realtime Compute for Apache Flink in exclusive mode.|Not supported|
|Supported data sources|-   DataHub
-   Log Service
-   Message Queue \(MQ\)
-   Message Queue for Apache Kafka

**Note:** For more information about source tables of Realtime Compute for Apache Flink, see [Overview of source tables](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md).

|-   DataHub
-   Log Service
-   MQ

**Note:** For more information about source tables of Realtime Compute for Apache Flink, see [Overview of source tables](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a source table/Overview of source tables.md). |
|Supported data outputs|-   AnalyticDB for MySQL
-   DataHub
-   Log Service
-   MQ
-   Tablestore
-   ApsaraDB RDS or DRDS
-   TSDB
-   HybridDB for MySQL
-   Kafka
-   ApsaraDB for HBase
-   Elasticsearch

**Note:** For more information about result tables of Realtime Compute for Apache Flink, see [Overview of result tables](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Overview of result tables.md).

|-   AnalyticDB for MySQL
-   DataHub
-   Log Service
-   MQ
-   Tablestore
-   ApsaraDB RDS or DRDS
-   TSDB
-   HybridDB for MySQL

**Note:** For more information about result tables of Realtime Compute for Apache Flink, see [Overview of result tables](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Overview of result tables.md). |
|Supported regions|-   Pay-as-you-go: China \(Hangzhou\), China \(Beijing\), China \(Shanghai\), and China \(Shenzhen\)
-   Subscription: China \(Hangzhou\), China \(Beijing\), China \(Shanghai\), China \(Shenzhen\), China \(Zhangjiakou\), China \(Hong Kong\), and Singapore \(Singapore\).

If you want to activate Realtime Compute for Apache Flink in exclusive mode in other regions,[submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).|China \(Shenzhen\) **Note:** As of December 24, 2019, Realtime Compute for Apache Flink in shared mode is no longer available. You cannot purchase projects in this mode. You can only scale out, scale in, or renew existing shared-mode projects. We recommend that you purchase the exclusive mode or semi-managed Flink mode of Realtime Compute for Apache Flink based on your business requirements. |
|Specification|Provides an independent real-time computing engine and allows each user to perform stream processing in an independent ECS cluster.|Provides a large cluster to allow users to share public resources.|
|Isolation|Provides strong isolation. Resources in an ECS instance such as networks and security groups are isolated from the resources of other users.|Provides weak isolation. Network resources cannot be isolated.|
|Customer groups|Big data teams that have advanced development technologies and demand for flexible and controllable development.|Teams or individuals that only want to realize streaming business and have no special requirements for the development process.|
|Cost|Has a higher cost than the shared mode. In high availability mode, each user must bear the cost of three additional master nodes.|Users only need to pay for the computing service.|
|Cluster management and maintenance|Requires cluster management and network configuration skills.|Cluster management is not required.|

