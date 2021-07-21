# 管理自定义Connectors

本文为您介绍如何管理Flink全托管自定义Connectors，包括创建、更新和删除。

在使用Flink SQL开发作业时，需要使用SQL Connectors连接您的源表、结果表和维表。Flink全托管产品支持云上常用的多种类型的Connectors，但相对于目前大数据常用的技术栈而言，仍然只是较少的一部分，为了支持您可以使用更多类型的Connectors，我们支持您自定义Connectors后上传使用。在使用过程中，您需要注意以下事项：

-   您需要根据社区定义的Connectors标准开发您自定义的Connectors，才能正确地被Flink全托管产品识别并使用，因此您需要明确的Connector Meta文件和申明Factory类。自定义Connector开发详情请参见[User-defined Sources & Sinks](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/table/sourceSinks.html)。
-   同种类型的Connectors仅可以上传一次。

为了避免JAR包依赖冲突，您需要注意以下几点：

-   Flink镜像和Pom依赖的Flink版本请保持一致。
-   请不要上传Runtime层的JAR包，即在依赖中添加`<scope>provided</scope>`。
-   其他第三方依赖请采用Shade方式打包，Shade打包详情参见[Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/index.html)。

## 创建自定义Connectors

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**作业开发**。

4.  在**Connectors**页签，单击![加号](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5741796061/p187440.png)图标。

    ![添加connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7482481261/p245238.png)

5.  上传自定义Connectors JAR文件。

    ![上传JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5924160161/p187443.png)

    您可以通过以下任何一种方式上传自定义Connectors JAR文件：

    -   **上传文件**：单击**选择文件**后，选择您的目标Connectors JAR文件。
    -   **外部URL**：输入外部URL地址。如果外部URL是OSS Bucket地址，则自定义Connectors文件必须位于sql-artifacts/namespaces/\{namespace\}目录下。
6.  上传完成后，单击**继续**。

    系统会对您上传的自定义Connectors内容进行解析。如果解析成功，您可以继续下一步。如果解析失败，请确认您上传的自定义Connectors代码是否符合Flink社区标准。

7.  单击**完成**。

    创建完成的自定义Connectors会出现在Connectors列表中。


## 更新自定义Connectors

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**作业开发**。

4.  在**Connectors**列表中，鼠标悬停在目标自定义Connectors名称上，单击![更新JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164582.png)。

5.  上传自定义Connectors JAR文件。

    ![修改connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5924160161/p187445.png)

    您可以通过以下任何一种方式上传自定义Connectors JAR文件：

    -   **上传文件**：单击**选择文件**后，选择您的目标Connectors JAR文件。
    -   **外部URL**：输入外部URL地址。如果外部URL是OSS Bucket地址，则自定义Connectors文件必须位于sql-artifacts/namespaces/\{namespace\}目录下。
6.  上传完成后，单击**继续**。

    系统会对您上传的自定义Connectors内容进行解析。如果解析成功，您可以继续下一步。如果解析失败，请确认您上传的自定义Connectors代码是否符合Flink社区标准。

7.  单击**完成**。

    创建完成的自定义Connectors会出现在Connectors列表中。


## 删除自定义Connectors

如果您的自定义Connectors不再使用，您可以按照以下步骤删除自定义Connectors。

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**作业开发**。

4.  在**Connectors**列表中，鼠标悬停在目标自定义Connectors名称上，单击![删除JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164586.png)图标。

5.  单击**确认**。


