# 管理自定义函数（UDF）

本文为您介绍如何管理Flink全托管自定义函数（UDF），包括注册、更新和删除。

## 注意事项

为了避免JAR包依赖冲突，您需要注意以下几点：

-   Flink镜像的版本请与Pom依赖中的Flink版本保持一致。
-   请不要上传Runtime层的JAR包，即在依赖中添加`<scope>provided</scope>`。
-   其他第三方依赖请采用Shade方式打包，Shade打包详情参见[Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/index.html)。

## 注册UDF

如果您的SQL需要使用UDF，您需要先注册您的UDF，才能在SQL中使用。

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏上，单击**作业开发**。

4.  在页面左上角，单击![添加](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164575.png)图标。

5.  上传UDF JAR文件。

    ![上传JAR文件](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0160160161/p133485.png)

    您可以通过以下任何一种方式上传UDF JAR文件：

    -   **上传文件**：单击**选择文件**后，选择您的目标UDF JAR文件。
    -   **外部URL**：输入外部URL地址。
    **说明：** 您的UDF JAR文件会被上传到您选择的OSS Bucket中的artifacts目录下。此外，Flink开发控制台会解析您UDF JAR文件中是否使用了Flink UDF、UDAF和UDTF接口的类，并自动提取类名，填充到Function Name字段中。

6.  单击**确认**。

    在SQL编辑器页面左侧**UDFs**列表，您可以看到所有注册成功的UDF。


## 更新UDF

如果您的UDF JAR文件中，新增了UDF或已注册了UDF的代码变更，您可以按照以下步骤更新UDF JAR文件。

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏上，单击**作业开发**。

4.  在**UDF JARs**列表中，鼠标悬停在目标UDF名称上，单击![更新JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164582.png)。

5.  上传UDF JAR文件。

    ![更新JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0396574161/p224239.png)

    您可以通过以下任何一种方式上传UDF JAR文件：

    -   **上传文件**：单击**选择文件**后，选择您的目标UDF JAR文件。
    -   **外部URL**：输入外部URL地址。
    **说明：**

    -   您上传的新UDF JAR文件中，必须包含当前已经注册UDF的所有类，不能出现某个已注册UDF的类消失的情况。
    -   只有重启作业或提交新作业时，才会使用新UDF JAR文件中的代码。如果引用该UDF JAR的作业正在运行，则依然会使用旧UDF JAR文件。
6.  单击**修改**。


## 删除UDF

如果您的UDF JAR文件不再使用，您可以按照以下步骤删除UDF。

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏上，单击**作业开发**。

4.  在**UDFs**列表中，鼠标悬停在目标UDF名称上，单击![删除JAR](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5957179951/p164586.png)图标。

    **说明：** 在删除UDF JAR文件前，请确定其注册的UDF没有被作业或SQL文件引用。

5.  选中**Drop以上所有Functions并且删除关联的JAR**。

    如果您要删除该UDF JAR文件，则需要删除该UDF JAR中所有注册的UDF，避免有脏数据残留。

6.  单击**确认**。


