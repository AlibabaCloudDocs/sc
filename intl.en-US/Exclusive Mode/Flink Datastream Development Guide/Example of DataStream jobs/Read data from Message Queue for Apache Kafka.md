---
keyword: [example of Message Queue for Apache Kafka data, DataStream, read data from Message Queue for Apache Kafka, Kafka]
---

# Read data from Message Queue for Apache Kafka

This topic describes how to run a Flink DataStream job to read data from Message Queue for Apache Kafka.

-   Java Development Kit \(JDK\) 8 is installed on your on-premises machine.
-   Maven 3.x is installed on your on-premises machine.
-   An integrated development environment \(IDE\) for Java or Scala is installed on your on-premises machine. We recommend that you use IntelliJ IDEA. The JDK and Maven are configured.
-   A Message Queue for Apache Kafka instance that resides in the same virtual private cloud \(VPC\) as your Realtime Compute for Apache Flink cluster in exclusive mode is created. A topic and a consumer group are created.

-   DataStream of Realtime Compute for Apache Flink is compatible with Apache Kafka 1.5.2. Message Queue for Apache Kafka is compatible with Apache Kafka. Therefore, you can directly use the Kafka connector in the Maven repository to access Message Queue for Apache Kafka.
-   Realtime Compute for Apache Flink in exclusive mode accesses Message Queue for Apache Kafka over a VPC. Simple Authentication and Security Layer \(SASL\) authentication is not required. If you access Message Queue for Apache Kafka over the Internet in your IDE, SASL authentication is required. For more information about the configurations of Message Queue for Apache Kafka, see [kafka-java-demo](https://github.com/AliwareMQ/aliware-kafka-demos/tree/master/kafka-java-demo).

**Note:** Only Blink 3.X supports this demo.

## Develop a job

1.  Download and decompress [alikafka-demo-master](https://github.com/RealtimeCompute/alikafka-demo) to your on-premises machine.

2.  In IntelliJ IDEA, choose **File** \> **Open** to open the decompressed package alikafka-demo-master.

3.  Double-click kafka.properties under the \\alikafka-demo-master\\src\\main\\resources directory to open the file. Then, change the values of the parameters bootstrap.servers, topic, and group.id to the values of the created Message Queue for Apache Kafka instance.

    ```
    ## Endpoints, which are obtained from the Message Queue for Apache Kafka console. 
    
    ## You can enter public and VPC endpoints for the bootstrap.servers parameter. However, if you use Realtime Compute for Apache Flink in exclusive mode, you must enter VPC endpoints. 
    bootstrap.servers=ip1:port,ip2:port,ip3:port
    
    ## The topic, which is created in the Message Queue for Apache Kafka console. 
    topic=your_topic
    
    ## The consumer group, which is created in the Message Queue for Apache Kafka console. 
    group.id=your_groupid
    ```

4.  Go to the directory where the pom.xml file is stored. Then, run the following command to package the file:

    ```
    mvn clean package
    ```

    A Java Archive \(JAR\) package named blink-datastreaming-1.0-SNAPSHOT.jar appears in the target directory, based on the artifactId parameter that you configured in the pom.xml file for your project. This indicates that job development is complete.


## Publish a job

For more information about how to publish a job, see [Publish a job](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Publish a job.md).

**Note:** Modify the configurations of blink.main.class, blink.job.name, and blink.main.jar as required.

The following example shows the job content:

```
-- The complete main class name, such as com.alibaba.realtimecompute.DatastreamExample. This field is required. 
blink.main.class=com.alibaba.blink.datastreaming.AliKafkaConsumerDemo

-- The name of the job. 
blink.job.name=alikafkaconsumerdemo

-- The resource name of the JAR package that contains the complete main class name, such as blink_datastream.jar. 
blink.main.jar=blink-datastreaming-1.0-snapshot.jar

-- The default state backend configuration. This field takes effect when the job code is not explicitly configured. 
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

-- The default checkpoint configuration. The configuration takes effect when the job code is not explicitly configured. 
blink.checkpoint.interval.ms=180000
```

**Note:** You can configure custom parameters. For more information, see [Set custom parameters](/intl.en-US/Exclusive Mode/Flink Datastream Development Guide/Set custom parameters.md).

## Verify the test results

1.  Send messages to Realtime Compute for Apache Flink in the Message Queue for Apache Kafka console.

2.  On the **Job Administration** page, view information in the taskmanager.out file of the sink node. In this example, the type of the sink node is print.

    If information similar to that shown in the following figure appears, Realtime Compute for Apache Flink has read data from Message Queue for Apache Kafka. The information depends on the messages sent from the Message Queue for Apache Kafka console.

    ![Data reading succeeded](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0365749951/p93774.png)


