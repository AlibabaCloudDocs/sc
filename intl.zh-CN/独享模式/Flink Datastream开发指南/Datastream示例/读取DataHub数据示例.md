---
keyword: [DataHub数据示例, Datastream, 读取DataHub]
---

# 读取DataHub数据示例

本文为您介绍如何使用Datastream作业读取阿里云DataHub数据。

-   本地安装了Java JDK 8。
-   本地安装了Maven 3.x。
-   本地安装了用于Java或Scala开发的IDE，推荐IntelliJ IDEA，且已配置完成JDK和Maven环境。
-   在DataHub上创建了Topic，并且Topic中存在[测试数据](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/159298/cn_zh/1585122460234/datahub_input.csv)。

    **说明：** 测试数据需要有4个字段，数据类型依次为STRING、STRING、DOUBLE和BIGINT。

-   已下载[datahub-demo-master](https://github.com/RealtimeCompute/datahub-demo)示例。

本文以Windows和Mac操作系统为例进行演示。

**说明：** 仅Blink3.x版本支持本示例。

## 开发

1.  实时计算Datastream完全兼容开源Flink 1.5.2版本。下载并解压[flink-1.5.2-compatible](https://github.com/alibaba/alibaba-flink-connectors/tree/flink-1.5.2-compatible)分支到本地。

    **说明：** 下载文件中的datahub-connector中同样实现了DataHub Sink功能，具体实现请参见下载文件中的DatahubSinkFunction.java和DatahubSinkFunctionExample.java。

2.  在CMD命令窗口，进入alibaba-flink-connectors-flink-1.5.2-compatible目录后，执行如下命令。

    ```
    mvn clean install
    ```

    可以看到如下结果。

    ![执行结果 ](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9265749951/p89088.png)

    命令执行成功后，datahub-connector对应的JAR包安装到本地的Maven仓库，通常默认安装在当前登录的用户文件夹下的.m2文件夹下。

3.  执行如下命令确认是否存在datahub-connector-0.1-SNAPSHOT-jar-with-dependencies.jar文件（将一个JAR及其依赖的三方JAR全部打到一个包中），后续会使用该JAR。

    -   Windows操作系统

        ```
        dir C:\Users\用户名\.m2\repository\com\alibaba\flink\datahub-connector\0.1-SNAPSHOT
        ```

        ![Windows操作系统执行结果](../images/p88530.png "Windows操作系统执行结果")

    -   Mac操作系统

        ```
        ls /Users/用户名/.m2/repository/com/alibaba/flink/datahub-connector/0.1-SNAPSHOT
        ```

4.  在Intellij IDEA中，单击**File** \> **Open**，打开刚才解压缩完成的datahub-demo-master包后，双击pom.xml查看代码。

    ![打开文件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9265749951/p91045.png)

    **说明：**

    -   IDE本地调试时需要将<scope\>provided</scope\>注释掉。
    -   在本示例中已默认使用<classifier\>jar-with-dependencies</classifier\>依赖步骤3中的datahub-connector-0.1-SNAPSHOT-jar-with-dependencies.jar。
5.  修改DatahubDemo.java文件中的DataHub相关参数。

    ```
    private static String endPoint = "inner endpoint";//内网访问。
    //private static String endPoint ="public endpoint";//公网访问（填写内网Endpoint，就不用填写公网Endpoint）。
    private static String projectName = "yourProject";
    private static String topicSourceName = "yourTopic";
    private static String accessId = "yourAK";
    private static String accessKey = "yourAS";
    private static Long datahubStartInMs = 0L;//设置消费的启动位点对应的时间。
    ```

6.  在下载文件pom.xml所在的目录执行如下命令打包文件。

    ```
    mvn clean package
    ```

    根据您的项目设置的artifactId，target目录下会出现blink-datastreaming-1.0-SNAPSHOT.jar，即代表完成了开发工作。


## 上线

请参见[上线](/intl.zh-CN/独享模式/Flink Datastream开发指南/作业提交.md)完成作业上线。

**说明：** 作业上线前，请在**开发**页面右侧的**资源配置**页签，配置源表的并发数，源表并发数不能大于源表的Shard数，否则作业启动后JM（Job Manager）报错。

本示例对应的作业内容如下。

```
--完整主类名，必填，例如com.alibaba.realtimecompute.DatastreamExample。
blink.main.class=com.alibaba.blink.datastreaming.DatahubDemo

--作业名称。
blink.job.name=datahub_demo

--包含完整主类名的JAR包资源名称，多个JAR包时必填，例如blink_datastream.jar。
blink.main.jar=${完整主类名jar包的资源名称}

--默认statebackend配置，当作业代码没有显式配置时生效。
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

--默认checkpoint配置，当作业代码没有显式配置时生效。
blink.checkpoint.interval.ms=180000
```

**说明：**

-   注意修改blink.main.class和blink.job.name。
-   您可以设置自定义参数，详情请参见[自定义参数](/intl.zh-CN/独享模式/Flink Datastream开发指南/自定义参数.md)。

## 验证

在**运维**页面，查看Sink节点的**taskmanager.out**信息，本示例中使用Print作为Sink。

如果出现如下结果，则表示已经成功读取了阿里云DataHub中的数据。

![验证](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9265749951/p88531.png)

## 常见问题

在作业运行时，如果界面上出现如下类似的错误，表示存在JAR包冲突。

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

