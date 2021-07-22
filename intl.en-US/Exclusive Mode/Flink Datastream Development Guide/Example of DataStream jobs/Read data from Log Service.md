---
keyword: [DataStream, read data from Log Service, Log Service]
---

# Read data from Log Service

This topic describes how to run a Flink DataStream job to read data from Log Service.

-   Java Development Kit \(JDK\) 8 is installed on your on-premises machine.
-   Maven 3.x is installed on your on-premises machine.
-   An integrated development environment \(IDE\) for Java or Scala is installed on your on-premises machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   A Logstore is created in Log Service, and test data exists in the Logstore.

Windows OS is used in this demo.

**Note:** Only Blink 3.X supports this demo.

## Develop a job

1.  Download and decompress the [SLS\_Demo-master.zip](https://github.com/RealtimeCompute/SLS_Demo) package to your on-premises machine.

2.  In IntelliJ IDEA, choose **File** \> **Open** to open the decompressed SLS\_Demo-master folder.

3.  Double-click the ConsumerSample.java file in the \\SLS\_Demo-master\\src\\main\\java\\com\\aliyun\\openservices\\log\\flink directory. Then, configure the parameters related to Log Service in the ConsumerSample.java file.

    ```
        private static final String SLS_ENDPOINT = "VPC endpoint";// Use the classic network endpoint or a virtual private cloud (VPC) endpoint in the production environment.
    //  private static final String SLS_ENDPOINT = "public endpoint";// Use the public endpoint in the test environment.
        private static final String ACCESS_KEY_ID = "yourAK";
        private static final String ACCESS_KEY_SECRET = "yourAS";
        private static final String SLS_PROJECT = "yourProject";
        private static final String SLS_LOGSTORE = "yourlogstore";
        // 1. Specify the start offset, which indicates the timestamp to start reading data from Log Service. The timestamp is measured in seconds. 2. To read both full and incremental data from Log Service, set the StartInMs parameter to Consts.LOG_BEGIN_CURSOR.
        // 3. To read only incremental data from Log Service, set the StartInMs parameter to Consts.LOG_END_CURSOR.
        private static final String StartInMs = Consts.LOG_END_CURSOR;
    ```

    **Note:** You must comment out <scope\>provided</scope\> when you perform local debugging in your IDE.

4.  Go to the directory where the pom.xml file is stored. Then, run the following command to package the file:

    ```
    mvn clean package
    ```

    A Java Archive \(JAR\) package named flink-log-connector-0.1.21-SNAPSHOT.jar appears in the target directory, based on the artifactId parameter that you configured in the pom.xml file for your project. This indicates that job development is complete.


## Publish a job

For more information about how to publish a job, see [Publish a job](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Publish a job.md).

**Note:** Before you publish the job, set the Parallelism parameter for the source table on the **Configurations** tab of the **Development** page. The parallelism setting of the source table cannot be greater than the number of shards in the source table. Otherwise, a JobManager error occurs after the job starts.

The following example shows the job content:

```
-- Required. The full name of the main class. 
--blink.main.class=com.aliyun.openservices.log.flink.ConsumerSample

-- The name of the job. 
blink.job.name=sls

-- The resource name of the JAR package that contains the full name of the main class. If multiple JAR packages exist, you must specify this parameter. 
--blink.main.jar=flink-log-connector-0.1.21-snapshot.jar

-- The default state backend configuration. This field takes effect when the job code is not explicitly configured. 
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

-- The default checkpoint configuration. The configuration takes effect when the job code is not explicitly configured. 
blink.checkpoint.interval.ms=180000
```

**Note:** You can configure custom parameters. For more information, see [Set custom parameters](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Set custom parameters.md).

## Verify the test results

On the Container Log tab of the **Job Administration** page, view information in the taskmanager.out file of the sink node. In this example, the type of the sink node is print.

If information shown in the following figure appears, Realtime Compute for Apache Flink reads the data from Log Service.

![Test results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1333860061/p140078.png)

