---
keyword: [自定义聚合函数, UDAF, Python自定义聚合函数]
---

# Python自定义聚合函数（UDAF）

本文为您介绍Python自定义聚合函数（UDAF）开发、注册和使用流程。

## 定义

自定义聚合函数（UDAF），将多条记录聚合成1条记录。其输入与输出是多对一的关系，即将多条输入记录聚合成一条输出值。

## 使用限制

由于Flink全托管产品受部署环境和网络环境等因素的影响，所以开发Python自定义函数时，需要注意以下限制：

-   仅支持开源Flink V1.12及以上版本。
-   Flink全托管集群上已预装了Python 3.7.9，因此需要您在Python 3.7.9版本开发代码。
-   Flink全托管运行环境使用的是JDK 1.8，如果Python作业中依赖第三方JAR包，请确保JAR包兼容JDK 1.8。
-   仅支持开源Scala V2.11版本，如果Python作业中依赖第三方JAR包，请确保使用Scala V2.11对应的JAR包依赖。

## UDAF开发

**说明：** Flink为您提供了Python UDX示例，便于您快速开发UDX。Flink Python UDX示例中包含了Python UDF、Python UDAF和Python UDTF的实现。本文以Windows操作系统为例，为您介绍如何进行UDAF开发。

1.  下载并解压[python\_demo-master](https://github.com/RealtimeCompute/python_demo)示例到本地。
2.  在PyCharm中，单击**file** \> **open**，打开刚才解压缩完成的python\_demo-master。
3.  双击打开\\python\_demo-master\\udx\\udafs.py后，根据您的业务，配置udafs.py。

    该示例中，weighted\_avg定义了当前数据和历史数据求含权重的均值的代码。

    ```
    from pyflink.common import Row
    from pyflink.table import AggregateFunction, DataTypes
    from pyflink.table.udf import udaf
    
    
    class WeightedAvg(AggregateFunction):
    
        def create_accumulator(self):
            # Row(sum, count)
            return Row(0, 0)
    
        def get_value(self, accumulator: Row) -> float:
            if accumulator[1] == 0:
                return 0
            else:
                return accumulator[0] / accumulator[1]
    
        def accumulate(self, accumulator: Row, value, weight):
            accumulator[0] += value * weight
            accumulator[1] += weight
    
        def retract(self, accumulator: Row, value, weight):
            accumulator[0] -= value * weight
            accumulator[1] -= weight
    
    
    weighted_avg = udaf(f=WeightedAvg(),
                        result_type=DataTypes.DOUBLE(),
                        accumulator_type=DataTypes.ROW([
                            DataTypes.FIELD("f0", DataTypes.BIGINT()),
                            DataTypes.FIELD("f1", DataTypes.BIGINT())]))
    ```

4.  在下载文件中udx所在的目录（即\\python\_demo-master目录）下执行如下命令打包文件。

    ```
    zip -r python_demo.zip udx
    ```

    \\python\_demo-master\\目录下会出现python\_demo.zip的ZIP包，即代表完成了UDAF开发工作。


## UDAF注册

UDAF注册过程，请参见[注册UDF](/intl.zh-CN/Flink全托管/Flink SQL开发指南/管理自定义函数（UDF）.md)。

## UDAF使用

在完成注册UDAF后，您就可以使用UDAF，详细的操作步骤如下。

1.  Flink SQL作业开发。详情请参见[作业开发](/intl.zh-CN/Flink全托管/Flink SQL开发指南/作业开发.md)。

    获取ASI\_UDAF\_Source表中a字段以b字段为权重的a字段当前数据和历史数据的均值，代码示例如下。

    ```
    CREATE TEMPORARY TABLE ASI_UDAF_Source (
      a   BIGINT,
      b   BIGINT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDAF_Sink (
      avg_value  DOUBLE
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDAF_Sink
    SELECT weighted_avg(a, b)
    FROM ASI_UDAF_Source;
    ```

2.  在**作业列表**中，单击目标作业名称**操作**列的**启动**。

    启动成功后，ASI\_UDAF\_Sink表每行会被插入ASI\_UDAF\_Source表中以b字段为权重的a字段当前数据和历史数据的均值。


