# 配置Session集群

您可以使用Session集群模式部署或调试作业，提高作业JM（Job Manager）资源利用率和提高作业启动速度。

Flink全托管支持**Per-Job集群**和**Session集群**两种集群模式。两种集群模式有以下区别：

-   **Per-Job集群**（默认）：作业之间资源隔离，每个作业都需要一个独立的JM，因此小任务JM的资源利用率较低。因此，适用于占用资源比较大或持续稳定运行的作业。
-   **Session集群**：多个作业可以复用相同的JM，可以提高JM资源利用率。因此适用于占用资源比较小或任务启停比较频繁的作业。

    **说明：**

    -   每个项目空间可以配置多个Session集群，但只能选择一个Session集群开启SQL Preview 。
    -   Session集群暂不支持开启自动调优功能。
    -   每个Session集群运行后，都会额外消耗0.5 CU资源。

## 创建Session集群

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV)。

2.  在**Flink全托管**页签，单击目标工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏上，选择**系统管理** \> **Session集群**。

4.  在页面右上角，单击**创建Session集群**。

5.  填写配置信息。

    ![创建session集群](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0997574161/p187373.png)

    参数解释如下表所示。

    |模块|配置项|说明|
    |--|---|--|
    |基础配置|名称|集群名称。|
    |状态|设置当前集群的期望运行状态：    -   STOPPED：当集群配置完成后保持停止状态，同样会停止所有在运行中的作业。
    -   RUNNING：当集群配置完成后保持运行状态。 |
    |设置为SQL Previews集群|将此Session集群设置为SQL Preview查询的资源集群。**说明：** 每个项目空间只能有一个Session集群开启SQL Preview，开启此设置将取消之前为SQL Preview设置的Session集群。 |
    |标签名|您可以在标签选项中添加作业标签，便于在总览页面快速定位作业。|
    |标签值|无。|
    |配置|Flink版本|仅支持Flink 1.10、Flink 1.11和Flink 1.12版本。**说明：** Python API作业需要选择Flink 1.11及以上版本。 |
    |Flink镜像标签|选择Flink镜像标签。**说明：** Python API作业需要选择1.11.2-vvr-2.1.1及以上版本。 |
    |Flink重启策略配置|该参数取值如下：    -   No Restarts：不重启。
    -   Fixed Delay：固定延迟。选择该选项后，您还需要填写尝试重启的次数和每次重启时间间隔。
    -   Failure Rate：故障率。选择该选项后，您还需要填写检测故障率的时间间隔，时间间隔内的最大失败次数和每次重启时间间隔。
**说明：** 如果您没有配置该参数，则按Apache Flink默认的重启策略，即当有Task失败时，如果没有开启Checkpoint，JobManager进程不会重启。如果开启了Checkpoint，则JobManager进程会重启。 |
    |Flink Master 容错配置（高可用性）|该参数取值如下：    -   Kubernets：Flink系统会将元数据保存到Kubernets中，供JobManager重启恢复时使用。
    -   None：Flink系统不会保存元数据，供JobManager重启恢复时使用。 |
    |其他配置|在此设置其他Flink配置。例如`taskmanager.numberOfTaskSlots: 1`。|
    |资源配置|Task Managers 数量|默认与并行度一致。|
    |Job Manager CPUs|默认值为1。|
    |Job Manager Memory|最小值为500Mi。单位建议使用Gi或Mi，例如，1024Mi或1.5Gi。|
    |Task Manager CPUs|默认值为1。|
    |Task Manager Memory|最小值为1Gi。单位建议使用Gi或Mi，例如，1024Mi或1.5Gi。|
    |日志配置|Root Log Level|TRACE、DEBUG、INFO、WARN和ERROR。|
    |Logger name|填写日志名称。|
    |Logger level|填写日志级别。|
    |Logging Profile|日志模板，可以选择系统模板，也可以选择用户配置。|

6.  单击**创建Session集群**。

    Session集群创建完成后，您就可以在创建Datastream作业或者SQL作业部署时，在**部署目标**选择您创建的Session集群。

    ![部署目标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3341796061/p187554.png)


