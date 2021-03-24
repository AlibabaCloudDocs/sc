---
keyword: [analytic database, MySQL, AnalyticDB for MySQL]
---

# Register an AnalyticDB for MySQL instance

This topic describes the parameters that are required to register an AnalyticDB for MySQL instance.

**Note:** This topic applies only to AnalyticDB for MySQL V2.0. Realtime Compute for Apache Flink does not allow you to register AnalyticDB for MySQL V3.0 to store result tables. To use the result tables of AnalyticDB for MySQL V3.0, you must create and reference the result tables in plaintext mode. For more information, see [Create an AnalyticDB for MySQL V3.0 result table](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create an AnalyticDB for MySQL V3.0 result table.md).

## Register storage resources

**Note:** Before you use Realtime Compute for Apache Flink to register storage resources, you must grant Realtime Compute for Apache Flink the permission to access these resources. For more information, see [Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode](/intl.en-US/Exclusive Mode/Preparation/Role authorization/Grant a RAM role to Realtime Compute for Apache Flink in exclusive mode.md).

1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).
2.  In the top navigation bar, click **Development**.
3.  In the left-side navigation pane of the Development page, click **Storage**.
4.  In the upper-left corner of the Storage page, click **+Registration and Connection**.
5.  In the **Register Data Store and Test Connection** dialog box, configure the storage parameters.
6.  Click **OK**.

## Storage parameters

|Parameter|Description|
|---------|-----------|
|URL|The URL of the AnalyticDB for MySQL database. You can use the URL to log on to the AnalyticDB for MySQL console. Enter the URL of the AnalyticDB for MySQL database based on the deployment mode of your Realtime Compute for Apache Flink: -   If Realtime Compute for Apache Flink is deployed in shared mode, enter the classic network URL of the AnalyticDB for MySQL database.
-   If Realtime Compute for Apache Flink is deployed in exclusive mode, enter the VPC URL of the AnalyticDB for MySQL database.

To view the URL, perform the following steps:

1.  Log on to the [AnalyticDB for MySQL console](https://ads.console.aliyun.com/?spm=a2c4g.11186623.2.23.2c952b809T8asM).
2.  In the left-side navigation pane, click Clusters. On the Clusters page, click the ID of the instance to go to the **Cluster Information** page.
3.  In the **Network Information** section, view the URL. |
|Database|The name of the AnalyticDB for MySQL database or the name of the AnalyticDB for MySQL instance.|
|AccessKey ID|The AccessKey ID of your Alibaba Cloud account.|
|AccessKey Secret|The AccessKey secret of your Alibaba Cloud account.|

