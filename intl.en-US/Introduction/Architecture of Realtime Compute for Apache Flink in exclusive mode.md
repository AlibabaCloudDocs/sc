---
keyword: [exclusive mode, architecture, benefits]
---

# Architecture of Realtime Compute for Apache Flink in exclusive mode

This topic describes the architecture of Realtime Compute for Apache Flink in exclusive mode.

## Architecture

The following figure shows the architecture of Realtime Compute for Apache Flink in exclusive mode.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4544749951/p33597.png)

-   For Realtime Compute for Apache Flink in exclusive mode, all your purchased ECS instances are fully hosted in the VPC of your Realtime Compute for Apache Flink cluster. In this mode, you cannot log on to these ECS instances.
-   When you create a Realtime Compute for Apache Flink cluster, Realtime Compute for Apache Flink applies for an elastic network interface \(ENI\) under your account. You can use this ENI to access all the resources in your VPC.
-   To enable Internet access for your Realtime Compute for Apache Flink cluster, you can bind a network address translation \(NAT\) gateway and an elastic IP address \(EIP\) to the ENI. For more information, see [Associate an EIP with a NAT gateway](/intl.en-US/Associate an EIP with a cloud instance/Associate an EIP with a NAT gateway.md) and [Overview](/intl.en-US/Associate an EIP with a cloud instance/Bind an EIP to a secondary ENI/Overview.md).
-   The ENI belongs to an independent security group under your account. To access the services of other security groups in the VPC, you must configure inbound and outbound rules for the security group.

**Note:** You are charged for the use of the ENI only when your Realtime Compute for Apache Flink cluster accesses the Internet.

## Benefits

-   End-to-end real-time data computing and development
    -   Provides a real-time data processing capability based on Flink SQL, which implements automatic data recovery. This ensures accurate data processing even if failures occur.
    -   Supports multiple built-in functions, such as string, date, and aggregate functions.
    -   Supports various window types, such as tumbling, sliding, and session windows.
    -   Implements accurate control over computing resources, which ensures resource isolation for jobs.
    -   Provides the following key performance metrics that are superior to those of open source Flink:
        -   The data computing latency can be sub-second.
        -   The throughput of a single job can reach millions of records per second. A single cluster can consist of thousands of servers.
    -   Deeply integrates various cloud data storage systems such as DataHub, Log Service, ApsaraDB RDS, Tablestore, and AnalyticDB for MySQL so that you can conveniently read and write data from and to these systems.
-   Fully managed real-time computing service
    -   Uses a fully managed stream computing engine.
    -   Allows you to run and query streaming data without the need to provision or manage any infrastructure.
    -   Allows you to activate streaming data processing services with one click.
    -   Integrates features such as data storage, data development, data O&M, and monitoring and alerts. This reduces both the trial and migration costs of stream processing.
    -   Effectively isolates and protects the managed and running services of different tenants.
-   Reduced manpower and cluster costs
    -   Significantly optimizes the SQL execution engine to deliver computing jobs that are more cost-effective than native Flink jobs.
    -   Significantly reduces development and operation costs, which are much lower than those of open source streaming frameworks.
-   High availability

    Assume that an ECS instance or a Realtime Compute for Apache Flink job fails. You can use the JobManager or TaskManager on an available ECS instance in the same zone to achieve high availability for jobs. You can also use the JobManager or TaskManager on an available ECS instance in another zone or region to achieve high availability across zones.

    ![High availability of clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4544749951/p100354.png)


