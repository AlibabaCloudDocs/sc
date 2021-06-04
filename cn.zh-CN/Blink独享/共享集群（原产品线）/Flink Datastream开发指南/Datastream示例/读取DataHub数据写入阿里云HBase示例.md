---
keyword: [读取DataHub, Datastream, 写入HBase]
---

# 读取DataHub数据写入阿里云HBase示例

本文为您介绍如何使用Datastream作业读取DataHub数据写入HBase。

-   本地安装了Java JDK 8。
-   本地安装了Maven 3.x。
-   本地安装了用于Java或Scala开发的IDE，推荐IntelliJ IDEA，且已配置完成JDK和Maven环境。
-   在DataHub上创建了Topic，并且Topic中存在测试数据。

    **说明：** 测试数据需要有3个字段，数据类型依次为BOOLEAN、STRING和STRING。

-   创建与实时计算独享模式同一地域下相同VPC的HBase示例，并创建表和列簇。通过Shell访问HBase集群步骤请参见[使用Shell访问](https://help.aliyun.com/document_detail/52056.html?spm=a2c4g.11174283.6.595.ccba363fBF1uOn)。

    **说明：**

    -   本示例为标准版HBase。
    -   实时计算集群IP需要添加至HBase白名单。

本文以Windows系统为例进行演示。

**说明：** 仅Blink 3.x版本支持本示例。

## 开发

1.  下载并解压[Hbase\_Demo-master](https://github.com/RealtimeCompute/Hbase_Demo)示例到本地。

2.  在IntelliJ IDEA中，单击**File** \> **Open**，打开刚才解压缩完成的Hbase\_Demo-master。

3.  双击打开\\Hbase\_Demo-master\\src\\main\\java\\Hbase\_Demo后，修改HbaseDemo.java文件中的DataHub与HBase相关参数。

    ```
    //DataHub相关参数
    //private static String endPoint ="public endpoint";//公网访问（填写内网Endpoint，就不用填写公网Endpoint）。
    private static String endPoint = "inner endpoint";//内网访问。
    private static String projectName = "yourProject";
    private static String topicSourceName = "yourTopic";
    private static String accessId = "yourAK";
    private static String accessKey = "yourAS";
    private static Long datahubStartInMs = 0L;//设置消费的启动位点对应的时间。
    //Hbase相关参数
    private static String zkQuorum = "yourZK";
    private static String tableName = "yourTable";
    private static String columnFamily = "yourcolumnFamily";
    ```

4.  在下载文件中pom.xml所在的目录执行如下命令打包文件。

    ```
    mvn package -Dcheckstyle.skip
    ```

    根据您的项目设置的artifactId，target目录下会出现Hbase\_Demo-1.0-SNAPSHOT-shaded.jar的JAR包，即代表完成了开发工作。


## 上线

请参见[上线](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink Datastream开发指南/作业提交.md)完成作业上线。

**说明：** 作业上线前，请在**开发**页面右侧的**资源配置**页签，配置源表的并发数，源表并发数不能大于源表的Shard数，否则作业启动后JM（Job Manager）报错。

本示例对应的作业内容如下。

```
--完整主类名，必填。
blink.main.class=Hbase_Demo.HbaseDemo

--作业名称。
blink.job.name=datahub_demo

--包含完整主类名的JAR包资源名称，多个JAR包时必填。
--blink.main.jar=Hbase_Demo-1.0-snapshot.jar

--默认statebackend配置，当作业代码没有显式配置时生效。
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

--默认checkpoint配置，当作业代码没有显式配置时生效。
blink.checkpoint.interval.ms=180000
```

**说明：** 您可以设置自定义参数，详情请参见[自定义参数](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink Datastream开发指南/自定义参数.md)。

## 验证

1.  在实时计算控制台发送测试数据至DataHub。

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

2.  连接HBase集群，详情请参见[使用Shell访问](https://help.aliyun.com/document_detail/52056.html?spm=a2c4g.11174283.6.595.ccba363fBF1uOn)。

3.  执行`scan 'hbase_sink'`查看写入数据。

    出现类似如下输出，则表示已经成功将DataHub数据写入阿里云HBase。

    ![验证结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8789287951/p139880.png)


## 常见问题

在作业运行时，如果界面出现如下类似的错误，表示存在JAR包冲突。

```
java.lang.AbstractMethodError：com.alibaba.fastjson.support.jaxrs.FastJsonAutoDiscoverable.configure(Lcom/alibaba/blink/shaded/datahub/javax/ws/rs/core/FeatureContext;)
```

![jar包冲突](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9265749951/p88532.png)

建议您使用maven-shade-plugin插件的Relocation功能，解决JAR包冲突的问题。

```
<relocations combine.self="override">
   <relocation>
       <pattern>org.glassfish.jersey</pattern>
       <shadedPattern>com.alibaba.blink.shaded.datahub.org.glassfish.jersey</shadedPattern>
   </relocation>
</relocations>
```

