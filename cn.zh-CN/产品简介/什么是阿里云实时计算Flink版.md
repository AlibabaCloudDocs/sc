---
keyword: [实时计算Flink版, 什么是阿里云实时计算Flink版]
---

# 什么是阿里云实时计算Flink版

阿里云实时计算Flink版（Alibaba Cloud Realtime Compute for Apache Flink，Powered by Ververica）是阿里云基于Apache Flink构建的企业级、高性能实时大数据处理系统，由Apache Flink创始团队官方出品，拥有全球统一商业化品牌，完全兼容开源Flink API，提供丰富的企业级增值功能。

## 产品优势

-   性能优越：作业可达百万级吞吐，计算可达秒级延迟，TPC-H性能测试可达开源引擎3~5倍。
-   功能强大：数十种作业指标监控，一站式开发界面，提供智能诊断系统，具有作业智能调优功能。
-   价格低廉：极致弹性体验，可按量付费，总资源费用低于自建。
-   稳定安全：服务SLA可达99.9%，集群计算无单点，故障可自动恢复，资源租户隔离，杜绝相互干扰。
-   品牌认证：Flink官方创始团队出品，中国信通院认证，进入Forrester象限的实时流计算产品。
-   兼容开源：提供最新Flink版本，与开源Flink接口100%兼容，实现业务平滑迁移上云。

## 产品架构

![产品架构](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1747324161/p141022.png)

## 与Apache Flink对比

![对比](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7219173161/p141885.png)

## 产品发展历史

阿里云实时计算 Flink 版主要经历了两个发展阶段：

1.  **Blink+Bayes**

    2016年~2019年为第一个发展阶段，阿里云实时计算Flink版以计算引擎Blink+开发平台Bayes为基础，对外提供Blink独享集群和Blink共享集群商业化产品。

    -   计算引擎Blink

        阿里巴巴基于Flink1.5版本进行功能开发和性能优化形成的内部企业版计算引擎，相比开源Flink，具备如下特点：

        -   主推SQL：在SQL上增加了Minibatch、LocalAGG、高效去重和TopN等功能优化。
        -   Runtime优化：增量Checkpoint实现、异步I/O实现、集群调度策略和Failover的策略优化。
        -   无缝对接云上10+种数据存储：包括日志服务SLS、数据总线DataHub、消息队列Kafka、表格存储Tablestore、云数据库RDS版、MaxCompute、云数据库Hbase版和ElasticSearch等。
        -   性能优越：部分场景性能相比开源Flink提升5~10倍。
        -   大规模部署：经过阿里巴巴双十一大规模场景下实践检验，稳定性和可靠性高。
    -   开发平台Bayes

        ![开发平台](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3993588951/p141887.png)

        阿里巴巴自研的实时计算开发平台，提供如下功能：

        -   数据存储
        -   本地调试
        -   告警监控
        -   图形化资源配置
        -   丰富的指标曲线
        -   Autoconf和Autoscale自动资源调优
        -   实时计算作业（Datastream和Flink SQL）提交运行
2.  **VVR+VVP**

    2019年至今为第二个发展阶段，阿里云实时计算Flink版以计算引擎VVR+开发平台VVP为基础，对外提供Flink全托管和Flink半托管商业化产品。

    ![vvp+vvr](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4993588951/p141026.png)

    -   计算引擎VVR（Ververica Runtime）

        阿里巴巴推出的完全兼容开源Flink的企业级计算引擎，相比开源Flink，提供如下增值功能：

        -   提供商业版的GeminiStateBackend
            -   采用创新的数据结构，提高随机查询、降低读磁盘I/O的性能。
            -   优化Cache策略，内存充足情况下热数据不落盘，并且Compaction后Cache不会失效。
            -   完全使用Java实现，消除RocksDB的JNI开销。
            -   使用堆外内存，并基于GeminiDB的特点实现高效的内存分配器，消除JVM GC带来的影响。
            -   支持异步增量Checkpoint，同步阶段只进行内存索引的拷贝，相较于RocksDB可以避免I/O带来的抖动。
            -   支持Local Recovery和Timer落盘。
        -   提供丰富的Connector：支持日志服务SLS、数据总线DataHub、消息队列Kafka、表格存储OTS等Connector，支持类型持续更新。
        -   完全兼容开源Flink，紧跟开源Flink版本更新。
    -   开发平台VVP（Ververica Platform）

        ![VVP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6208763061/p141886.png)

        阿里巴巴和Apache Flink创始团队合力打造的实时计算开发平台，提供如下功能：

        -   告警监控
        -   支持Savepoint
        -   丰富的指标曲线
        -   AutoPilot自动资源调优
        -   实时计算作业（Datastream和Flink SQL）提交运行

## 相关文档

-   实时计算Flink版产品形态及选型请参见[产品形态](/cn.zh-CN/产品简介/产品形态.md)。
-   实时计算Flink版应用场景请参见[应用场景](/cn.zh-CN/产品简介/应用场景.md)。

