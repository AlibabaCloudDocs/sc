---
keyword: [read data from DataHub, write data to ApsaraDB for HBase]
---

# Read data from DataHub and write data to ApsaraDB for HBase

This topic describes how to use Flink jobs to synchronize data from DataHub to ApsaraDB for HBase in real time. Flink 1.10 on Windows is used in the example.

-   Development tools
    -   Java Development Kit \(JDK\) 8 is installed on your machine.
    -   Maven 3.x is installed on your machine.
    -   An integrated development environment \(IDE\) for Java or Scala is installed on your machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   Service activation
    -   A VPC is created. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Create a VPC.md).
    -   Object Storage Service \(OSS\) is activated. For more information, see [Activate OSS](https://help.aliyun.com/document_detail/31884.html#task-njz-hf4-tdb).
    -   A Flink Serverless cluster is created. For more information, see [Procedure for activating the service](/intl.en-US/Fully managed Flink/Procedure for activating the service.md).
    -   A DataHub project that belongs to the same VPC within the same region as the Flink Serverless cluster is created. For more information, see [DataHub product homepage](https://www.aliyun.com/product/datahub?spm=5176.124785.J_8058803260.214.76c83094YQ0pel).
    -   An ApsaraDB for HBase cluster that belongs to the same VPC within the same region as the Flink Serverless cluster is created. ApsaraDB for HBase Standard Edition is used in this example. For more information, see [ApsaraDB for HBase product homepage](https://www.aliyun.com/product/hbase?spm=a2c4g.11174283.2.1.7469363fCl3WwP).
-   Permission configuration

    The IP address of the Flink Serverless cluster is added to the whitelist of the ApsaraDB for HBase cluster. For more information, see [Configure a whitelist](/intl.en-US/Fully managed Flink/Preparations/Configure a whitelist.md).

-   Upstream and downstream storage
    -   A project and a topic are created in the DataHub console. In this example, the topic contains the a field of the BOOLEAN type, the b field of the STRING type, and the c field of the STRING type.
    -   The hbase\_sink table and the f1 column family are created in the ApsaraDB for HBase console. For more information, see [Use HBase Shell to access ApsaraDB for HBase](https://www.alibabacloud.com/help/doc-detail/52056.htm?spm=a2c63.l28256.b99.52.5a9c5f1f6Mm6bu) and visit [Create a table and column family](http://hbase.apache.org/1.1/book.html?spm=a2c4g.11186623.2.18.6abe5cc1OSJzJj#shell_exercises).

## Create a job

1.  Download and decompress [Hbase\_ASI\_Demo-main](https://github.com/RealtimeCompute/Hbase_ASI_Demo) to your machine.

2.  Open IntelliJ IDEA and choose **File** \> **Open**. Select the extracted Hbase\_ASI\_Demo-main folder and click OK.

3.  Double-click the HbaseDemo.java file in the \\Hbase\_ASI\_Demo-main\\src\\main\\java\\Hbase\_Demo directory. Then, configure the parameters related to DataHub and ApsaraDB for HBase in the HbaseDemo.java file.

    ```
    // Configure the parameters related to DataHub.
    //private static String endPoint ="public endpoint";// Access DataHub over the Internet. If you enter an internal endpoint, you do not need to enter the public endpoint.
    private static String endPoint = "inner endpoint";// Access DataHub over an internal network.
    private static String projectName = "yourProject";
    private static String topicSourceName = "yourTopic";
    private static String accessId = "yourAK";
    private static String accessKey = "yourAS";
    private static Long datahubStartInMs = 0L;// Set the time that corresponds to the start offset.
    // Configure the parameters related to ApsaraDB for HBase.
    private static String zkQuorum = "yourZK";
    private static String tableName = "yourTable";
    private static String columnFamily = "yourcolumnFamily";
    ```

    |Storage type|Parameter|Description|
    |------------|---------|-----------|
    |DataHub|endPoint|The endpoint used to access the DataHub project.**Note:** If you enter an internal endpoint, you do not need to enter the public endpoint. |
    |projectName|The name of the DataHub project.|
    |topicSourceName|The name of the DataHub topic.|
    |accessId|The AccessKey ID of your Alibaba Cloud account.|
    |accessKey|The AccessKey secret of your Alibaba Cloud account.|
    |HBase|zkQuorum|The **ZooKeeper address** of your ApsaraDB for HBase cluster. You can use one of the following methods to view the **ZooKeeper address**:    -   In the left-side navigation pane of the ApsaraDB for HBase cluster, click **Database Connection**. View the ZooKeeper address in the **Connection Information** section.
    -   Open the hbase-site.xml file in the <HBase installation directory\>/conf/ directory, and view the hbase.zookeeper.quorum configuration in the file. |
    |tableName|The name of the HBase table that you created.|
    |columnFamily|The column family name of the HBase table that you created.|

4.  Go to the directory where the pom.xml file is stored. Then, run the following command to package the file:

    ```
    mvn package -Dcheckstyle.skip
    ```

    Based on the artifactId information in your pom.xml file, the Hbase\_Demo-1.0-SNAPSHOT.jar package appears in the \\Hbase\_ASI\_Demo-main\\target\\ directory. This indicates that the job is created.


## Submit a job

For more information about how to submit a job, see [Submit a job](/intl.en-US/Fully managed Flink/Flink DataStream Developer Guide/Submit a job.md).

## View the results

1.  Construct your test data.

    Create and start an SQL job in the Flink Serverless cluster, and then send the result data to DataHub as test data for this best practice.

    ```
    CREATE TEMPORARY TABLE datagen_source (
       a BOOLEAN
    ) WITH (
       'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE datahub_sink (
       a BOOLEAN,
       b VARCHAR,
       c VARCHAR
    ) WITH (
        'connector' = 'datahub',
        'endPoint' = '<yourEndpoint>',    -- Replace yourEndpoint with the endpoint of the Datahub project that you created.
        'project' = '<yourProject>',   -- Replace yourProject with the name of the Datahub project that you created.
        'project' = '<yourProject>',   -- Replace yourProject with the name of the Datahub project that you created.
        'accessId'='<yourAccessId>',  -- Replace yourAccessId with the AccessKey ID of your Alibaba Cloud account.   
        'accessKey'='<yourAccessKey>'  -- Replace yourAccessKey with the AccessKey secret of your Alibaba Cloud account.
    );
    
    INSERT INTO datahub_sink
    SELECT
        a,'rowkey3' as b,'123' as c
    FROM datagen_source;
    ```

2.  Connect to the ApsaraDB for HBase cluster.

    For more information, see [Use HBase Shell to access ApsaraDB for HBase](https://www.alibabacloud.com/help/doc-detail/52056.htm?spm=a2c63.l28256.b99.52.5a9c5f1f6Mm6bu).

3.  Run the `scan 'hbase_sink'` command to query the data written to ApsaraDB for HBase.

    If an output similar to the one in the following figure is displayed, Realtime Compute for Apache Flink writes the DataHub data to ApsaraDB for HBase.

    ![Test results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0033860061/p139880.png)


