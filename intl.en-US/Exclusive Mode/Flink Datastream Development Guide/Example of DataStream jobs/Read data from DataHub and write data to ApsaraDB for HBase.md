---
keyword: [read data from DataHub, DataStream, write data to ApsaraDB for HBase]
---

# Read data from DataHub and write data to ApsaraDB for HBase

This topic describes how to run a Flink DataStream job to read data from DataHub and write data to ApsaraDB for HBase.

-   Java Development Kit \(JDK\) 8 is installed on your on-premises machine.
-   Maven 3.x is installed on your on-premises machine.
-   An integrated development environment \(IDE\) for Java or Scala is installed on your on-premises machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   A topic is created in DataHub, and test data exists in the topic.

    **Note:** The test data must contain three fields, whose data types are BOOLEAN, STRING, and STRING in sequence.

-   An ApsaraDB for HBase cluster is created. The ApsaraDB for HBase cluster resides in the same region and the same virtual private cloud \(VPC\) as your Realtime Compute for Apache Flink cluster in exclusive mode. A table with several column families is created in the ApsaraDB for HBase cluster. To use Shell to access ApsaraDB for HBase,see [Use HBase Shell to access ApsaraDB for HBase](https://www.alibabacloud.com/help/doc-detail/52056.htm?spm=a2c63.l28256.b99.52.5a9c5f1f6Mm6bu).

    **Note:**

    -   ApsaraDB for HBase Standard Edition is used in this topic.
    -   You must add the IP address of your Realtime Compute for Apache Flink cluster to a whitelist of ApsaraDB for HBase.

The Windows OS is used in this demo.

**Note:** Only Blink 3.X supports this demo.

## Develop a job

1.  Download and decompress the [Hbase\_Demo-master.zip](https://github.com/RealtimeCompute/Hbase_Demo) package to your on-premises machine.

2.  In IntelliJ IDEA, choose **File** \> **Open** to open the decompressed packeage Hbase\_Demo-master.

3.  Double-click the HbaseDemo.java file in the \\Hbase\_Demo-master\\src\\main\\java directory. Then, configure the parameters related to DataHub and ApsaraDB for HBase in the HbaseDemo.java file.

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

4.  Go to the directory where the pom.xml file is stored. Then, run the following command to package the file:

    ```
    mvn package -Dcheckstyle.skip
    ```

    A Java Archive \(JAR\) package named Hbase\_Demo-1.0-SNAPSHOT-shaded.jar appears in the target directory, based on the artifactId parameter that you configured in the pom.xml file for your project. This indicates that job development is complete.


## Publish a job

For more information about how to publish a job, see [Publish a job](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Publish a job.md).

**Note:** Before you publish the job, set the Parallelism parameter for the source table on the **Configurations** tab of the **Development** page. The parallelism setting of the source table cannot be greater than the number of shards in the source table. Otherwise, a JobManager error occurs after the job starts.

The following example shows the job content:

```
-- Required. The full name of the main class. 
blink.main.class=Hbase_Demo.HbaseDemo

-- The name of the job. 
blink.job.name=datahub_demo

-- The resource name of the JAR package that contains the full name of the main class. If multiple JAR packages exist, you must specify this parameter. 
--blink.main.jar=Hbase_Demo-1.0-snapshot.jar

-- The default state backend configuration. This field takes effect when the job code is not explicitly configured. 
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

-- The default checkpoint configuration. The configuration takes effect when the job code is not explicitly configured. 
blink.checkpoint.interval.ms=180000
```

**Note:** You can configure custom parameters. For more information, see [Set custom parameters](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Set custom parameters.md).

## Verify the test results

1.  Send test data to DataHub in the Realtime Compute for Apache Flink console.

    ```
    CREATE TABLE kafka_src (
       a BOOLEAN
    ) WITH (
       type = 'random'
    );
    
    CREATE TABLE event_logs (
       `a` BOOLEAN,
        b  VARCHAR,
       `c` VARCHAR
    ) WITH (
        type = 'datahub',
        endPoint = '<yourEndpoint>',
        project = '<yourProject>',
        topic = '<yourTopic>',
        accessId='<yourAccessId>',    
        accessKey='<yourAccessKey>'
    );
    
    INSERT INTO event_logs
    SELECT
        a,'rowkey3' as b,'123' as c
    FROM kafka_src;
    ```

2.  Connect to the ApsaraDB for HBase cluster.For more information about how to connect to an ApsaraDB for HBase cluster, see [Use HBase Shell to access ApsaraDB for HBase](https://www.alibabacloud.com/help/doc-detail/52056.htm?spm=a2c63.l28256.b99.52.5a9c5f1f6Mm6bu).

3.  Run the `scan 'hbase_sink'` command to query the data written to ApsaraDB for HBase.

    If information similar to that in the following figure appears, Realtime Compute for Apache Flink writes the DataHub data to ApsaraDB for HBase.

    ![Test results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0033860061/p139880.png)


## FAQ

If an error similar to the following one appears when a job is running, a JAR package conflict occurs. What do I do?

```
java.lang.AbstractMethodError:com.alibaba.fastjson.support.jaxrs.FastJsonAutoDiscoverable.configure(Lcom/alibaba/blink/shaded/datahub/javax/ws/rs/core/FeatureContext;)
```

![JAR package conflict](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0033860061/p88532.png)

We recommend that you use the relocation feature of maven-shade-plugin to resolve the JAR package conflict.

```
<relocations combine.self="override">
   <relocation>
       <pattern>org.glassfish.jersey</pattern>
       <shadedPattern>com.alibaba.blink.shaded.datahub.org.glassfish.jersey</shadedPattern>
   </relocation>
</relocations>
```

