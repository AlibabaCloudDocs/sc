---
keyword: [自定义表值函数, UDTF, Python自定义表值函数]
---

# Python自定义表值函数（UDTF）

本文为您介绍Python自定义表值函数（UDTF）的开发、注册和使用流程。

## 定义

自定义表值函数（UDTF），将0个、1个或多个标量值作为输入参数（可以是变长参数）。表值函数可以返回任意数量的行作为输出，而不仅是1个值。返回的行可以由1个或多个列组成。调用一次函数输出多行或多列数据。与自定义的标量函数类似，但与标量函数不同。

## 使用限制

由于Flink全托管产品受部署环境和网络环境等因素的影响，所以开发Python自定义函数时，需要注意以下限制：

-   仅支持开源Flink V1.12及以上版本。
-   Flink全托管集群上已预装了Python 3.7.9，因此需要您在Python 3.7.9版本开发代码。
-   Flink全托管运行环境使用的是JDK 1.8，如果Python作业中依赖第三方JAR包，请确保JAR包兼容JDK 1.8。
-   仅支持开源Scala V2.11版本，如果Python作业中依赖第三方JAR包，请确保使用Scala V2.11对应的JAR包依赖。

## UDTF开发

**说明：** Flink为您提供了Python UDX示例，便于您快速开发UDX。Flink Python UDX示例中包含了Python UDF、Python UDAF和Python UDTF的实现。本文以Windows操作系统为例，为您介绍如何进行UDTF开发。

1.  下载并解压[python\_demo-master](https://github.com/RealtimeCompute/python_demo)示例到本地。
2.  在PyCharm中，单击**file** \> **open**，打开刚才解压缩完成的python\_demo-master。
3.  双击打开\\python\_demo-master\\udx\\udtfs.py后，根据您的业务，修改udtfs.py文件内容。

    该示例中，split定义了将一行字符串按照竖线（\|）分割成多列字符串的代码。

    ```
    from pyflink.table import DataTypes
    from pyflink.table.udf import udtf
    
    @udtf(result_types=[DataTypes.STRING(), DataTypes.STRING()])
    def split(s: str):
        splits = s.split("|")
        yield splits[0], splits[1]
    ```

4.  在下载文件中udx所在的目录（即\\python\_demo目录）下执行如下命令打包文件。

    ```
    zip -r python_demo.zip udx
    ```

    \\python\_demo\\目录下会出现python\_demo.zip的ZIP包，即代表完成了UDTF开发工作。


## UDTF注册

UDTF注册过程，请参见[注册UDF](/cn.zh-CN/Flink全托管/Flink SQL开发指南/管理自定义函数（UDF）.md)。

## UDTF使用

在完成UDTF注册后，您就可以使用UDTF，详细的操作步骤如下。

1.  Flink SQL作业开发。详情请参见[作业开发](/cn.zh-CN/Flink全托管/Flink SQL开发指南/作业开发.md)。

    ASI\_UDTF\_Source表中每行字符串的message字段与字符串aa按照竖线（\|）连接之后，按照竖线（\|）分割成多列，代码示例如下。

    ```
    CREATE TEMPORARY TABLE ASI_UDTF_Source (
      `message`  VARCHAR
    ) WITH (
      'connector'='datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDTF_Sink (
      name  VARCHAR,
      place  VARCHAR
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDTF_Sink
    SELECT name,place
    FROM ASI_UDTF_Source,lateral table(split(concat_ws('|', `message`, 'aa'))) as T(name,place);
    ```

2.  在**作业运维**页面，单击目标作业名称**操作**列的**启动**。

    启动成功后，ASI\_UDTF\_Sink表会被插入两列数据，这两列数据是ASI\_UDTF\_Source表中message和aa字段按照竖线（\|）连接后，再按照竖线（\|）分割成多列的字符。


