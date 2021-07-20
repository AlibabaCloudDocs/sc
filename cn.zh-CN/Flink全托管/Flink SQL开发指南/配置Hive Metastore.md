# 配置Hive Metastore

本文为您介绍如何在Flink全托管模式下配置Hive Metastore功能。

您可以将Hive Metastore配置文件和Hadoop依赖存放至对象存储OSS控制台指定目录后，再在Flink全托管开发控制台上配置Hive Metastore功能。配置成功后，您就可以在Flink全托管开发控制台上直接使用DML创建业务逻辑，获取Hive中表的元数据信息，无需再使用DDL语句声明相应的表信息。Hive Metastore可以作为Stream作业和Batch作业的源表或结果表。

## 注意事项

使用Hive Metastore功能需要注意以下几点：

-   支持自建Hive Metastore。
-   VVP 2.3.0及以上版本支持Hive Metastore。
-   VVP 2.3.0版本仅支持Hive Metastore 2.3.6版本；VVP 2.3.0以上版本支持Hive Metastore 2.2.0~2.3.7。
-   Hive Metastore不支持Kerberos鉴权。
-   一个Flink全托管实例仅支持一个Hive Metastore，多项目无法设置多Hive Metastore。
-   Hive Metastore只读，即您不能通过Flink全托管开发控制台在Hive中创建物理表。

## 配置Hive Metastore服务

1.  连通Hadoop集群和Flink全托管的VPC。

    您可以使用云解析PrivateZone产品连通Hadoop集群和Flink全托管的VPC，详情请参见[解析器（Resolver）](/cn.zh-CN/用户指南/解析器（Resolver）.md)。网络连通后，Flink全托管就可以使用Hadoop集群的配置文件访问Hadoop集群。

2.  在OSS控制台新建文件夹，并将Hive配置文件和Hadoop依赖放置目标路径。

    1.  登录[OSS管理控制台](https://oss.console.aliyun.com/)。

    2.  在左侧导航栏，单击**Bucket列表**。

    3.  单击目标Bucket名称。

    4.  在左侧导航栏，单击**文件管理**。

    5.  新建文件夹，用于存放Hive配置文件和Hadoop依赖。

        -   Hive配置文件路径：oss://$\{bucket\}/artifacts/namespaces/$\{ns\}/$\{hms\}/hive-conf-dir/
        -   Hadoop依赖路径：oss://$\{bucket\}/artifacts/namespaces/$\{ns\}/$\{hms\}/hadoop-conf-dir/
        其中：

        -   $\{bucket\} ：您Flink全托管实例使用的Bucket名称。
        -   $\{ns\}：您要使用Hive Metastore功能的Flink全托管项目名称。
        -   $\{hms\}：您在Flink全托管上显示的Hive Metastore名称。
    6.  将您的Hive配置文件（hive-site.xml）放置到hive-conf-dir目录下。

        在上传hive-site.xml文件前，请您先检查下配置文件中hive.metastore.uris参数配置是否符合下列要求。

        ```
        <property>
            <name>hive.metastore.uris</name>
            <value>thrift://xx.yy.zz.mm:9083</value>
            <description>Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.</description>
         </property>
        ```

        其中`xx.yy.zz.mm`为Hive的内网IP或者公网IP，如果您将hive.metastore.uris填写为hostname，则VVP远程访问Hive时，hive.metastore.uris参数值会被解析失败并报错`UnknownHostException`。

    7.  将您的以下配置文件放置到hadoop-conf-dir目录下。

        -   hive-site.xml
        -   core-site.xml
        -   hdfs-site.xml
        -   mapred-site.xml
        -   其他文件，例如Hive作业使用的压缩包。
3.  在Flink全托管开发控制台上配置Hive Metastore服务。

    1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

    2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

    3.  在左侧导航栏，单击**作业开发**。

    4.  在页面左上角，单击**新建**，文件类型选择SQL。

    5.  在文本编辑区域，输入创建Hive Metastore的命令。

        ```
        CREATE CATALOG ${HMS Name} WITH (
            'type' = 'hive',
            'default-database' = 'default',
            'hive-version' = '<hive-version>',
            'hive-conf-dir' = '<hive-conf-dir>',
            'hadoop-conf-dir' = '<hadoop-conf-dir>'
        );
        ```

        其中：

        -   $\{HMS Name\}：Hive Metastore名称。
        -   type：Connector类型，固定值为hive。
        -   default-database ：默认数据库名称。
        -   hive-version：Hive Metastore版本号。

            **说明：** Flink全托管兼容的Hive Metastore版本为2.2.0~2.3.7，hive-version的配置情况如下：

            -   如果Hive Metastore版本为2.0.0~2.2.0，则hive-version需要指定为2.2.0。
            -   如果Hive Metastore版本为2.3.0~2.3.7，则hive-version需要指定为2.3.6。
        -   hive-conf-dir：存放Hive配置文件的目录。
        -   hadoop-conf-dir：存放Hadoop依赖的目录。
    6.  单击**运行**。

        Hive Metastore服务配置完成后，您就可以在作业中引用Hive Metastore表信息，作为结果表和维表，无需声明表的DDL。Hive Metastore表名称格式为$\{hive-catlog-name\}.$\{hive-db-name\}.$\{hive-table-name\}。

        此外，如果您想停用Hive Metastore服务，详情请参见[删除Hive Metasore服务](#section_h4x_5t3_488)。


## 查看Hive元数据

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**作业开发**。

4.  单击**Schemas**页签。

5.  在顶部菜单栏下拉框中，切换到目标Hive Metastore。

    ![Hive](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2851481261/p177086.png)

6.  在**Tables**列查看不同数据库下的表和字段信息。


## 删除Hive Metasore服务

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏，单击**作业开发**。

4.  在页面左上角，单击**新建**，文件类型选择SQL。

5.  在文本编辑区域，输入以下命令。

    `Drop CATALOG ${HMS Name}`

    其中，HMS Name为您要删除的在Flink全托管开发控制台上显示的Hive Metastore名称。

    **说明：** 删除Hive Metastore服务不会影响已运行的作业，但对未上线或者作业需要暂停恢复的作业，均产生影响，请您谨慎操作。

6.  单击**运行**。


