# 自定义标量函数（UDF）

本文为您介绍如何为Flink自定义标量函数（UDF）开发、注册和使用流程。

## 定义

自定义标量函数（UDF）将0个、1个或多个标量值映射到一个新的标量值。输入与输出是一对一的关系，即读入一行数据，写出一条输出值。

## UDF开发

**说明：** Flink为您提供了UDX示例，便于您快速开发业务。Flink UDX示例中包含UDF、UDAF和UDTF的实现，示例中已为您配置对应版本的开发环境，您无需进行环境搭建。

1.  下载并解压[ASI\_UDX\_Demo](https://github.com/RealtimeCompute/ASI_UDX)示例到本地。

    解压完成后，会生成ASI\_UDX-main文件夹。其中：

    -   pom.xml：项目级别的配置文件，主要描述了项目的Maven坐标、依赖关系，开发者需要遵循的规则、缺陷管理系统，组织和Licenses，以及其他所有的项目相关因素。
    -   \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDF\\ASI\_UDF.java：自定义标量函数（UDF）示例的Java代码。
2.  在IntelliJ IDEA中，单击**file** \> **open**，打开刚才解压缩完成的ASI\_UDX-main。
3.  双击打开\\ASI\_UDX-main\\src\\main\\java\\ASI\_UDF后，根据您的业务，配置ASI\_UDF.java。

    该示例中，ASI\_UDF.java已配置了获取每条数据中从begin~end位的字符的代码。

    ```
    package ASI_UDF;
    
    import org.apache.flink.table.functions.ScalarFunction;
    
    public class ASI_UDF extends ScalarFunction {
        public String eval(String s, Integer begin, Integer end) {
            return s.substring(begin, end);
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

    \\ASI\_UDX-main\\target\\目录下会出现ASI\_UDX-1.0-SNAPSHOT.jar的JAR包，即代表完成了UDF开发工作。


## UDF注册

UDF注册过程，请参见[管理自定义函数（UDF）](/intl.zh-CN/Flink全托管/Flink SQL开发指南/管理自定义函数（UDF）.md)。

## UDF使用

在注册UDF完成后，您就可以使用UDF，详细的操作步骤如下。

1.  Flink SQL作业开发。详情请参见[作业开发](/intl.zh-CN/Flink全托管/Flink SQL开发指南/作业开发.md)。

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


