---
keyword: [自定义标量函数, UDF, Python自定义标量函数]
---

# Python自定义标量函数（UDF）

本文为您介绍Python自定义标量函数（UDF）的开发、注册和使用流程。

## 定义

自定义标量函数（UDF）将0个、1个或多个标量值映射到一个新的标量值。输入与输出是一对一的关系，即读入一行数据，写出一条输出值。

## 使用限制

由于Flink全托管产品受部署环境和网络环境等因素的影响，所以开发Python自定义函数时，需要注意以下限制：

-   仅支持开源Flink V1.12及以上版本。
-   Flink全托管集群上已预装了Python 3.7.9，因此需要您在Python 3.7.9版本开发代码。
-   Flink全托管运行环境使用的是JDK 1.8，如果Python作业中依赖第三方JAR包，请确保JAR包兼容JDK 1.8。
-   仅支持开源Scala V2.11版本，如果Python作业中依赖第三方JAR包，请确保使用Scala V2.11对应的JAR包依赖。

## UDF开发

**说明：** Flink为您提供了Python UDX示例，便于您快速开发UDX。Flink Python UDX示例中包含了Python UDF、Python UDAF和Python UDTF的实现。本文以Windows操作系统为例，为您介绍如何进行UDF开发。

1.  下载并解压[python\_demo-master](https://github.com/RealtimeCompute/python_demo)示例到本地。
2.  在PyCharm中，单击**file** \> **open**，打开刚才解压缩完成的python\_demo-master。
3.  双击打开\\python\_demo-master\\udx\\udfs.py后，根据您的业务，修改udfs.py。

    该示例中，sub\_string定义了获取每条数据中从begin~end位的字符的代码。

    ```
    from pyflink.table import DataTypes
    from pyflink.table.udf import udf
    
    
    @udf(result_type=DataTypes.STRING())
    def sub_string(s: str, begin: int, end: int):
        return s[begin:end]
    ```

4.  在下载文件中udx所在的目录（即\\python\_demo-master目录）下执行如下命令打包文件。

    ```
    zip -r python_demo.zip udx
    ```

    \\python\_demo-master\\目录下会出现python\_demo.zip的ZIP包，即代表完成了Python UDF的开发工作。


## UDF注册

UDF注册过程，请参见[注册UDF](/cn.zh-CN/Flink全托管/Flink SQL开发指南/管理自定义函数（UDF）.md)。

## UDF使用

在完成注册UDF后，您就可以使用UDF，详细的操作步骤如下。

1.  Flink SQL作业开发。详情请参见[作业开发](/cn.zh-CN/Flink全托管/Flink SQL开发指南/作业开发.md)。

    获取ASI\_UDF\_Source表中a字段中每行字符串中第2~4位的字符，代码示例如下。

    ```
    CREATE TEMPORARY TABLE ASI_UDF_Source (
      a VARCHAR,
      b INT,
      c INT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDF_Sink (
      a VARCHAR
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDF_Sink
    SELECT ASI_UDF(a,2,4)
    FROM ASI_UDF_Source;
    ```

2.  在**作业列表**中，单击目标作业名称**操作**列的**启动**。

    启动成功后，ASI\_UDF\_Sink表每行会被插入ASI\_UDF\_Source表中a字段每行字符串的第2~4位字符。


