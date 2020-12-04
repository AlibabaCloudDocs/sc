# 管理自定义Connectors

本文为您介绍如何管理Flink全托管自定义Connectors，包括创建、更新和删除。

在使用Flink SQL开发作业时，需要使用SQL Connectors连接您的源表、结果表和维表。Flink全托管产品支持云上常用的多种类型的Connectors，但相对于目前大数据常用的技术栈而言，仍然只是较少的一部分，为了支持您可以使用更多类型的Connectors，我们支持您自定义Connectors后上传使用。在使用过程中，您需要注意以下事项：

-   您需要根据社区定义的Connectors标准开发您自定义的Connectors，才能正确的被Flink全托管产品识别并使用，因此您需要明确的Connector Meta文件和申明Factory类。
-   如果您提交的自定义Connectors类型，Flink全托管产品已经内置，则将以您提交的自定义Connectors来启动作业，如果您后续删除了自定义的Connectors，则会使用Flink全托管产品内置的Connectors。
-   同种类型的Connectors仅可以上传一次。

## 创建自定义Connectors

1.  登录[实时计算统一控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**应用** \> **SQL编辑器**。

4.  单击**Connectors**页签。

5.  单击![加号](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5741796061/p187440.png)图标。

    ![添加connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5741796061/p187441.png)

6.  上传自定义Connectors JAR文件。

    ![上传JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5741796061/p187443.png)

    您可以通过以下任意一种方式上传自定义Connectors JAR文件：

    -   单击灰色区域，打开您的目标自定义Connectors JAR文件。
    -   拖动自定义Connectors JAR文件到灰色区域，即可上传。
7.  上传完成后，单击**继续**。

    系统会对您上传的自定义Connectors内容进行解析。如果解析成功，您可以继续下一步。如果解析失败，请确认您上传的自定义Connectors代码是否符合Flink社区标准。

8.  单击**完成**

    创建完成的自定义Connectors会出现在Connectors列表中。


## 更新自定义Connectors

1.  登录[实时计算统一控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**应用** \> **SQL编辑器**。

4.  单击**Connectors**页签。

5.  在**Connectors**列表中，鼠标悬停在目标自定义Connectors名称上，单击![更新JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164582.png)。

6.  上传自定义Connectors JAR文件。

    ![修改connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5741796061/p187445.png)

    您可以通过以下任意一种方式上传自定义Connectors JAR文件：

    -   单击灰色区域，打开您的目标自定义Connectors JAR文件。
    -   拖动自定义Connectors JAR文件到灰色区域，即可上传。
7.  上传完成后，单击**继续**。

    系统会对您上传的自定义Connectors内容进行解析。如果解析成功，您可以继续下一步。如果解析失败，请确认您上传的自定义Connectors代码是否符合Flink社区标准。

8.  单击**完成**

    创建完成的自定义Connectors会出现在Connectors列表中。


## 删除自定义Connectors

如果您的自定义Connectors不再使用，您可以按照以下步骤删除自定义Connectors。

1.  登录[实时计算统一控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**应用** \> **SQL编辑器**。

4.  单击**Connectors**页签。

5.  在**Connectors**列表中，鼠标悬停在目标自定义Connectors名称上，单击![删除JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164586.png)图标。

6.  单击**确认**。


