# 自定义聚合函数（UDAF）

本文为您介绍如何为Flink自定义聚合函数（UDAF）开发、注册和使用流程。

## 定义

自定义聚合函数（UDAF），将多条记录聚合成1条记录。其输入与输出是多对一的关系，即将多条输入记录聚合成一条输出值。

## UDAF开发

**说明：** Flink为您提供了UDX示例，便于您快速开发业务。Flink UDX示例中包含UDF、UDAF和UDTF的实现，示例中已为您配置对应版本的开发环境，您无需进行环境搭建。

1.  下载并解压[ASI\_UDX\_Demo](https://github.com/RealtimeCompute/ASI_UDX)示例到本地。

    解压完成后，会生成ASI\_UDX-main文件夹。其中：

    -   pom.xml：项目级别的配置文件，主要描述了项目的Maven坐标、依赖关系，开发者需要遵循的规则、缺陷管理系统，组织和Licenses，以及其他所有的项目相关因素。
    -   \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDAF\\ASI\_UDAF.java：自定义聚合函数（UDAF）示例的Java代码。
2.  在IntelliJ IDEA中，单击**file** \> **open**，打开刚才解压缩完成的ASI\_UDX-main。
3.  双击打开\\ASI\_UDX-main\\src\\main\\java\\ASI\_UDAF后，根据您的业务，配置ASI\_UDAF.java。

    该示例中，ASI\_UDAF.java已配置了当前数据和历史数求和的代码。例如，第1个数据是1，第2个数据是2，第3个数据是3，则第一个结果是1，第2个结果是3，第3个结果是6。

    ```
    package ASI_UDAF;
    
    import org.apache.flink.table.functions.AggregateFunction;
    public class ASI_UDAF{
        public static class AcSum {
            public long sum;
        }
    
        public static class WeightedSum extends AggregateFunction<Long, AcSum> {
    
            @Override
            public Long getValue(AcSum acSum) {
                return acSum.sum;
            }
            @Override
            public AcSum createAccumulator() {
                AcSum acCount = new AcSum();
                acCount.sum = 0;
                return acCount;
            }
            public void accumulate(AcSum acc, long acSum) {
                acc.sum += acSum;
            }
        }
    }
    ```

4.  双击打开\\ASI\_UDX-main\\后，配置pom.xml。

    该示例中，pom.xml文件已配置了Flink 1.11版依赖的主要JAR包信息。如果您的业务：

    -   不依赖其他JAR包：不用配置pom.xml文件，继续下一步。
    -   依赖其他JAR包：在pom.xml文件中添加您所需依赖的JAR包信息。
    Flink 1.11版依赖的主要JAR包如下。

    ```
    <dependencies>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-streaming-java_2.12</artifactId>
                <version>1.11.0</version>
                <!--<scope>provided</scope>-->
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-table</artifactId>
                <version>1.11.0</version>
                <type>pom</type>
                <!--<scope>provided</scope>-->
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-core</artifactId>
                <version>1.11.0</version>
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-table-common</artifactId>
                <version>1.11.0</version>
            </dependency>
        </dependencies>
    ```

5.  在下载文件中pom.xml所在的目录执行如下命令打包文件。

    ```
    mvn package -Dcheckstyle.skip
    ```

    \\ASI\_UDX-main\\target\\目录下会出现ASI\_UDX-1.0-SNAPSHOT.jar的JAR包，即代表完成了UDAF开发工作。


## UDAF注册

UDAF注册过程，请参见[管理自定义函数（UDF）](/intl.zh-CN/Flink全托管/Flink SQL开发指南/管理自定义函数（UDF）.md)。

## UDAF使用

在注册UDAF完成后，您就可以使用UDAF，详细的操作步骤如下。

1.  Flink SQL作业开发。详情请参见[t1894286.md\#](/intl.zh-CN/Flink全托管/Flink SQL开发指南/作业开发.md)。

    获取ASI\_UDAF\_Source表中a字段当前数据和历史数据之和，代码示例如下。

    ```
    CREATE TEMPORARY TABLE ASI_UDAF_Source (
      a   BIGINT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDAF_Sink (
      sum  BIGINT
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDAF_Sink
    SELECT `ASI_UDAF$WeightedSum`(a)
    FROM ASI_UDAF_Source;
    ```

2.  在**作业列表**中，单击目标作业名称**操作**列的**启动**。

    启动成功后，ASI\_UDAF\_Sink表每行会被插入ASI\_UDAF\_Source表中a字段当前数据和历史数据之和。


