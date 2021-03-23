---
keyword: [Kafka数据示例, Datastream, 读取Kafka, Kafka]
---

# 读取Kafka数据示例

本文为您介绍如何使用Datastream作业读取阿里云Kafka数据。

-   本地安装了Java JDK 8。
-   本地安装了Maven 3.x。
-   本地安装了用于Java或Scala开发的IDE，推荐IntelliJ IDEA，且已配置完成JDK和Maven环境。
-   创建与实时计算独享模式相同VPC的Kafka实例，并创建了Topic和Consumer Group。

-   实时计算Datastream完全兼容开源Flink 1.5.2版本，阿里云Kafka兼容开源Kafka，因此可以直接使用Maven仓库里的Kafka Connetor来连接阿里云Kafka。
-   实时计算独享模式通过内网接入Kafka，无需进行SASL认证鉴权。如果您在本地IDE上通过公网方式接入阿里云Kafka，则需要进行SASL认证鉴权，具体配置请参见[kafka-java-demo](https://github.com/AliwareMQ/aliware-kafka-demos/tree/master/kafka-java-demo)。

**说明：** 仅Blink3.x版本支持本示例。

## 开发

1.  下载并解压[alikafka-demo-master](https://github.com/RealtimeCompute/alikafka-demo)示例到本地。

2.  在Intellij IDEA中，单击**File** \> **Open**，打开刚才解压缩完成的alikafka-demo-master。

3.  双击打开\\alikafka-demo-master\\src\\main\\resources目录下的kafka.properties后，修改bootstrap.servers、topic和group.id为您创建的Kafka实例对应值。

    ```
    ## 接入点，通过控制台获取。
    
    #  注意公网和VPC接入点的区别，在Blink独享模式下需要使用默认接入点，而非SSL接入点。
    bootstrap.servers=ip1:port,ip2:port,ip3:port
    
    ## Topic，通过控制台创建。
    topic=your_topic
    
    ## ConsumerGroup，通过控制台创建。
    group.id=your_groupid
    ```

4.  在下载文件中pom.xml所在的目录执行如下命令打包文件。

    ```
    mvn clean package
    ```

    根据您的项目设置的artifactId，target目录下会出现blink-datastreaming-1.0-SNAPSHOT.jar的JAR包，即代表完成了开发工作。


## 上线

请参见[上线](/intl.zh-CN/独享模式/Flink Datastream开发指南/作业提交.md)完成作业上线。

**说明：** 注意修改blink.main.class、blink.job.name和blink.main.jar。

本示例对应的作业内容如下。

```
--完整主类名，必填，例如com.alibaba.realtimecompute.DatastreamExample。
blink.main.class=com.alibaba.blink.datastreaming.AliKafkaConsumerDemo

--作业名称。
blink.job.name=alikafkaconsumerdemo

--包含完整主类名的JAR包资源名称，多个JAR包时必填，例如blink_datastream.jar。
blink.main.jar=blink-datastreaming-1.0-snapshot.jar

--默认statebackend配置，当作业代码没有显式配置时生效。
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

--默认checkpoint配置，当作业代码没有显式配置时生效。
blink.checkpoint.interval.ms=180000
```

**说明：** 您可以设置自定义参数，详情请参见[自定义参数](/intl.zh-CN/独享模式/Flink Datastream开发指南/自定义参数.md)。

## 验证

1.  在Kafka控制台发送消息。

2.  在实时计算的**运维**界面，查看Sink节点的taskmanager.out输出结果，本示例中使用Print作为Sink。

    出现类似如下输出（具体内容以实际发送的消息内容为准），则表示已经成功读取了阿里云Kafka中的数据。

    ![读取成功](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0365749951/p93774.png)


