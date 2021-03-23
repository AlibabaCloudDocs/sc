---
keyword: [Datastream, 读取SLS, 日志服务]
---

# 读取日志服务SLS示例

本文为您介绍如何使用Datastream作业读取阿里云日志服务SLS数据示例。

-   本地安装了Java JDK 8。
-   本地安装了Maven 3.x。
-   本地安装了用于Java或Scala开发的IDE，推荐IntelliJ IDEA，且已配置完成JDK和Maven环境。
-   SLS上已创建了logstore ，并且logstore中存在测试数据。

本文以Windows操作系统为例进行演示。

**说明：** 仅Blink 3.x版本支持本示例。

## 开发

1.  下载并解压[SLS\_Demo](https://github.com/RealtimeCompute/SLS_Demo)示例到本地。

2.  在Intellij IDEA中，单击**File** \> **Open**，打开刚才解压缩完成的SLS\_Demo-master。

3.  双击打开SLS\_Demo-master\\src\\main\\java\\com\\aliyun\\openservices\\log\\flink\\ConsumerSample后，修改ConsumerSample.java文件中的SLS的相关参数。

    ```
        private static final String SLS_ENDPOINT = "VPC endpoint";//线上使用经典网络及VPC Endpoint
    //  private static final String SLS_ENDPOINT = "public endpoint";//本地测试使用 公网Endpoint
        private static final String ACCESS_KEY_ID = "yourAK";
        private static final String ACCESS_KEY_SECRET = "yourAS";
        private static final String SLS_PROJECT = "yourProject";
        private static final String SLS_LOGSTORE = "yourlogstore";
        //1、启动位点秒级的时间戳读取数据;2、读取全量加增量数据Consts.LOG_BEGIN_CURSOR;
        //3、读取增量数据Consts.LOG_END_CURSOR
        private static final String StartInMs = Consts.LOG_END_CURSOR;
    ```

    **说明：** IDE本地调试注意将<scope\>provided</scope\>注释掉。

4.  在下载文件中pom.xml所在的目录执行如下命令打包文件。

    ```
    mvn clean package
    ```

    根据您的项目设置的artifactId，target目录下会出现flink-log-connector-0.1.21-SNAPSHOT.jar的JAR包，即代表完成了开发工作。


## 上线

请参见[上线](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink Datastream开发指南/作业提交.md)完成作业上线。

**说明：** 作业上线前，请在**开发**页面右侧的**资源配置**页签，配置源表的并发数，源表并发数不能大于源表的Shard数，否则作业启动后JM（Job Manager）报错。

本示例对应的作业内容如下。

```
--完整主类名，必填。
--blink.main.class=com.aliyun.openservices.log.flink.ConsumerSample

--作业名称。
blink.job.name=sls

--包含完整主类名的JAR包资源名称，多个JAR包时必填。
--blink.main.jar=flink-log-connector-0.1.21-snapshot.jar

--默认statebackend配置，当作业代码没有显式配置时生效。
state.backend.type=niagara
state.backend.niagara.ttl.ms=129600000

--默认checkpoint配置，当作业代码没有显式配置时生效。
blink.checkpoint.interval.ms=180000
```

**说明：** 您可以设置自定义参数，详情请参见[自定义参数](/cn.zh-CN/Blink独享/共享集群（原产品线）/Flink Datastream开发指南/自定义参数.md)。

## 验证

在实时计算Flink版**运维**界面，查看Sink节点的taskmanager.out输出结果，本示例中使用Print作为Sink。

如果出现如下结果，则表示已经成功读取了阿里云SLS中的数据。

![测试结果](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9789287951/p140078.png)

