# Blink-3.3.0

本文为您介绍实时计算公告内容，包括版本重大功能发布和产品变更介绍。

## 版本重大功能

-   小CU模式
    -   新增初始plan设置CU预期：生成plan时，作业的初始并发度会根据用户设置的CU预期进行缩放。
    -   新增小CU模式资源配置：当用户设置的CU预期很小，每个Vertex缩小成1个并发都无法放下时，会触发小CU模式，自动进行压缩，同时将所有Vertex调度到一个TaskManager上，减少框架资源开销。
-   DataHub
    -   写入DataHub支持通过hashFields的配置，使相同列值的记录写入同一个下游Shard。
    -   修复Blink 3.2版本读取closed shard的DataHub数据Failover，且任务不能恢复的问题。

## 产品变更

-   Auto Scale变更
    -   Autoscale最大资源设置语义修改：Max CU限制从作业整体资源的上限调整为Plan的资源上线，以解决Max CU限制，导致作业无法启动的问题。Max CU限制调整后，作业实际使用的资源可能会超过Max CU。

    -   Autoscale添加可选作业参数配置，可以手动关闭scale down功能，保证作业稳定性。配置项：`healthmanager.resource.scale.down.enabled`（资源scale down开关）和 `healthmanager.parallelism.scale.down.enabled`（并发度scale down开关）。
    -   新增支持Datastream作业手动设置资源，开启Autoscale。从Blink3.3版本开始，Datastream作业支持手动编辑资源Plan，同时允许开启Autoscale。Datastream的Autoscale目前仅处于试用阶段。
-   FirstRow on Rowtime

    支持您按照Rowtime字段读取FirstRow，去重后保持time字段的Rowtime属性，即您可以在去重节点后继续使用Window。

-   SQL文件大小写变更

    该改动将忽略SQL文件中的大小写（大小写不敏感），导致部分作业在编译时报错。该错误表明您在同一段代码中，使用大小写来区分变量或标识符。

    -   示例

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5874359951/p54208.png)

    -   报错信息

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6874359951/p54209.jpeg)


