---
keyword: [Prometheus监控报警, 监控告警, 报警, 监控]
---

# 配置监控告警（在ARMS控制台中配置）

Flink全托管支持配置作业运行的监控告警，作业运行过程中，如果触发了监控规则，系统会给您发送告警消息，便于您及时发现并处理异常。本文为您介绍如何在应用实时监控服务ARMS控制台上配置Prometheus监控报警。

已开通应用实时监控服务ARMS，详情请参见[开通和升级ARMS](/intl.zh-CN/快速入门/开通和升级ARMS.md)。

在ARMS产品中配置监控告警时，需要指定集群、类型、大盘和PromQL等信息，但此方式操作复杂、上手成本高、告警配置效率低。您可以在Flink全托管开发控制台，快速配置监控告警，操作简单、易用，效率高，详情请参见[配置监控告警（在Flink全托管开发控制台配置）](/intl.zh-CN/Flink全托管/运维管理/配置监控告警（在Flink全托管开发控制台配置）.md)。

1.  登录[实时计算控制台](https://realtime-compute.console.aliyun.com/regions/cn-shanghai)。

2.  在**Flink全托管**页签，单击对应工作空间**操作**列下的**开发控制台**。

3.  在左侧导航栏上，单击**作业运维**。

4.  单击目标作业名称。

    **说明：** 请选择作业**期望状态**和**当前状态**都为**RUNNING**的作业。

5.  在**作业概览**页面右上角，单击**指标**。

    ![指标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3165635161/p247662.png)

6.  在左侧导航栏上，单击**Alerting**。

    ![Alerting](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3043449951/p111850.png)

7.  在左侧导航栏上，单击**报警管理** \> **报警策略管理**。

8.  在页面右上角，单击**创建报警** \> **Prometheus**。

    ![Prometheus](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3043449951/p110436.png)

9.  在报警对话框中，配置如下参数。

    ![相关信息](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3043449951/p110437.png)

    |配置项|说明|
    |---|--|
    |**报警名称**|例如，网络接收压力报警。|
    |**集群**|选择目标**集群**。|
    |**类型**|选择为**grafana**。|
    |**大盘**|选择为**Flink**。|
    |**图表**|按您的业务需要选择**图表**。|
    |**报警规则和历史**|选中**同时满足下述规则**。|
    |**最近N分钟**|例如：N=5时网络接收数据字节（MB）的平均值大于等于3时则报警。**说明：** 一个Grafana图表中可能有A、B和C等多条曲线数据，您可根据需求设置监控其中的一条线。 |
    |**PromQL**|编辑或重新输入**PromQL**语句。**说明：** PromQL语句中包含的`$`符号会导致报错，您需要删除包含`$`符号的语句中`=`左右两边的参数。例如：将`sum (rate (container_network_receive_bytes_total{instance=~"^$HostIp.*"}[1m]))`修改为`sum (rate (container_network_receive_bytes_total[1m]))`。 |
    |**通知方式**|支持短信、邮件和钉钉机器人三种通知方式。钉钉机器人报警设置方法，请参见[设置钉钉机器人报警](/intl.zh-CN/大盘和报警/设置钉钉机器人报警.md)。

**说明：** **安全设置**请至少选中**自定义关键词**，且关键词至少设置为**报警**才能收到告警信息。 |
    |**通知对象**|在**全部联系组**中单击联系人分组的名称，如果该联系人分组出现在**已选联系组**中，则设置成功。|

10. 单击**保存**。


