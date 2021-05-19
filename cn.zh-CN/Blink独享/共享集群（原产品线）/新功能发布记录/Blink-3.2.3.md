# Blink-3.2.3

为了给您带来更好的开发交互体验， 实时计算发布了Blink-3.2.3版本，本文为您介绍版本优化点和修复的问题。

## 优化

-   优化Vertex Topology页面与作业资源配置界面展示不一致问题。
-   优化3.2.1版本数据曲线页面Task ID的ID名称不展示问题。
-   优化运维界面TaskManager.log日志中乱码问题。
-   优化3.2.1版本GC日志覆盖上线调试数据问题。
-   优化独享集群AutoScale需要增加作业参数才能开启问题。
-   优化作业运行代码为LEFT JOIN，运维界面Vertex拓扑图中显示为INNER JOIN问题。
-   优化代码编辑有误时不定位至具体的一行问题。

## 问题修复

-   REGEXP\_EXTRACT函数参数为null或正则表达式不合法时，返回null，与实际不符。
-   开发代码中使用反斜线的正则表达式后，使用用分号编译报错。
-   上线调试运行结果打印至taskmanager.out中，与实际不符。
-   LEFT JOIN维表，JOIN时无维表标识不报错。
-   Time类型的数据写入ADS结果表出现异常。
-   3.2.1版本MaxCompute维表使用TIMESTAMP类型进行JOIN，作业运行报错。
-   3.2.1和3.2.2版本使用`minibatch`作业参数，作业运行报错。
-   CEP语法检测正常，作业运行报错。
-   云数据库HBase结果表的`maxRetryTimes`参数未生效。
-   Kafka源表消费在Kafka端未展示对应的Group ID。
-   只写入一个VARBINARY类型的数据至MQ，运行报错。

