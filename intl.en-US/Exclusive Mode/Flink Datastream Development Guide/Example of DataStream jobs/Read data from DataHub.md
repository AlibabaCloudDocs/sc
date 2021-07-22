---
keyword: [example of DataHub data, DataStream, read data from DataHub]
---

# Read data from DataHub

This topic describes how to run a Flink DataStream job to read data from DataHub.

-   Java Development Kit \(JDK\) 8 is installed on your on-premises machine.
-   Maven 3.x is installed on your on-premises machine.
-   An integrated development environment \(IDE\) for Java or Scala is installed on your on-premises machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   A topic is created in DataHub, and [test data](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/159298/cn_zh/1585122460234/datahub_input.csv) exists in the topic.

    **Note:** The test data must contain four fields, whose data types are STRING, STRING, DOUBLE, and BIGINT in sequence.

-   [datahub-demo-master](https://github.com/RealtimeCompute/datahub-demo) is downloaded.

Windows OS and macOS are used in this demo.

**Note:** Only Blink 3.X supports this demo.

## Develop a job

1.  DataStream of Realtime Compute for Apache Flink is compatible with Apache Flink 1.5.2. Download and decompress [flink-1.5.2-compatible](https://github.com/alibaba/alibaba-flink-connectors/tree/flink-1.5.2-compatible) to your on-premises machine.

    **Note:** datahub-connector in the downloaded file functions as the DataHub sink. For more information, see DatahubSinkFunction.java and DatahubSinkFunctionExample.java in the downloaded file.

2.  In the command window, go to the alibaba-flink-connectors-flink-1.5.2-compatible directory and run the following command:

    ```
    mvn clean install
    ```

    The following figure shows the command results.

    ![Results ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9265749951/p89088.png)

    If the command is successfully run, the Java Archive \(JAR\) package that corresponds to datahub-connector is installed in the Maven repository on your on-premises machine. By default, the package is saved in the .m2 folder under the folder of the current logon user.

3.  Run the following command to check whether the package datahub-connector-0.1-SNAPSHOT-jar-with-dependencies.jar exists. This package contains a JAR package and its dependent third-party JAR packages, which will be used in subsequent operations.

    -   Windows OS

        ```
        dir C:\Users\Username\.m2\repository\com\alibaba\flink\datahub-connector\0.1-SNAPSHOT
        ```

        ![Command results in the Windows OS](../images/p88530.png "Command results in the Windows OS")

    -   macOS

        ```
        ls /Users/Username/.m2/repository/com/alibaba/flink/datahub-connector/0.1-SNAPSHOT
        ```

4.  In IntelliJ IDEA, choose **File** \> **Open** to open the decompressed package datahub-demo-master. Then, double-click pom.xml to view the code.

    ![Open the file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9265749951/p91045.png)

    **Note:**

    -   When you perform local debugging for IDE, you must comment out <scope\>provided</scope\>.
    -   In this example, <classifier\>jar-with-dependencies</classifier\> in datahub-connector-0.1-SNAPSHOT-jar-with-dependencies.jar in Step 3 is used by default.
5.  Modify DataHub-related parameters in the DatahubDemo.java file.

    ```
    private static String endPoint = "inner endpoint";// Indicates access over an internal network. 
    //private static String endPoint ="public endpoint";// Indicates access over the Internet. If you have entered an internal endpoint, you do not need to enter the public endpoint. 
    private static String projectName = "yourProject";
    private static String topicSourceName = "yourTopic";
    private static String accessId = "yourAK";
    private static String accessKey = "yourAS";
    private static Long datahubStartInMs = 0L;// Set the time that corresponds to the start offset. 
    ```

6.  Go to the directory where the pom.xml file is saved and run the following command to package the file:

    ```
    mvn clean package
    ```

    A JAR package named blink-datastreaming-1.0-SNAPSHOT.jar appears in the destination directory, based on the artifactId parameter that you configured in the pom.xml file for your project. This indicates that job development is complete.


## Publish a job

For more information about how to publish a job, see [Publish a job](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Publish a job.md).

**Note:** Before you publish a job, set the Parallelism parameter for the source table on the **Configurations** tab of the **Development** page. The parallelism setting of the source table cannot be greater than the number of shards in the source table. Otherwise, a JobManager error occurs after the job starts.

The following example shows the job content:

```
-- The complete main class name, such as com.alibaba.realtimecompute.DatastreamExample. This field is required. 
blink.main.class=com.alibaba.blink.datastreaming.DatahubDemo

-- The name of the job. 
blink.job.name=datahub_demo

-- The resource name of the JAR package that contains the complete main class name, such as blink_datastream.jar. 
blink.main.jar=${Resource name of the JAR package that contains the complete main class name}

-- The default state backend configuration. This field takes effect when the job code is not explicitly configured. 
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

-- The default checkpoint configuration. The configuration takes effect when the job code is not explicitly configured. 
blink.checkpoint.interval.ms=180000
```

**Note:**

-   Modify blink.main.class and blink.job.name as required.
-   You can configure custom parameters. For more information, see [Set custom parameters](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Set custom parameters.md).

## Verify the test results

On the Container Log tab of the **Job Administration** page, view information in the **taskmanager.out** file of the sink node. In this example, the type of the sink node is print.

If the information shown in the following figure appears, Realtime Compute for Apache Flink has read data from DataHub.

![Verify the test results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9265749951/p88531.png)

## FAQ

If an error similar to the following error appears when a job is running, a JAR package conflict occurs. What do I do?

```
java.lang.AbstractMethodError: com.alibaba.fastjson.support.jaxrs.FastJsonAutoDiscoverable.configure(Lcom/alibaba/blink/shaded/datahub/javax/ws/rs/core/FeatureContext;)
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

