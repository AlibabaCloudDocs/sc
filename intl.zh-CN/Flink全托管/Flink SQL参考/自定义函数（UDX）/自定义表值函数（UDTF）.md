# 自定义表值函数（UDTF）

本文为您介绍如何为Flink自定义表值函数（UDTF）开发、注册和使用流程。

## 定义

自定义表值函数（UDTF），自定义表值函数，将0个、1个或多个标量值作为输入参数（可以是变长参数）。与自定义的标量函数类似，但与标量函数不同。表值函数可以返回任意数量的行作为输出，而不仅是1个值。返回的行可以由1个或多个列组成。调用一次函数输出多行或多列数据。

## UDTF开发

**说明：** Flink为您提供了UDX示例，便于您快速开发业务。Flink UDX示例中包含UDF、UDAF和UDTF的实现，示例中已为您配置对应版本的开发环境，您无需进行环境搭建。

1.  下载并解压[ASI\_UDX\_Demo](https://github.com/RealtimeCompute/ASI_UDX)示例到本地。

    解压完成后，会生成ASI\_UDX-main文件夹。其中：

    -   pom.xml：项目级别的配置文件，主要描述了项目的Maven坐标、依赖关系，开发者需要遵循的规则、缺陷管理系统，组织和Licenses，以及其他所有的项目相关因素。
    -   \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDF\\ASI\_UDTF.java：自定义表值函数（UDTF）示例的Java代码。
2.  在IntelliJ IDEA中，单击**file** \> **open**，打开刚才解压缩完成的ASI\_UDX-main。
3.  双击打开\\ASI\_UDX-main\\src\\main\\java\\ASI\_UDTF后，根据您的业务，修改ASI\_UDTF.java文件内容。

    该示例中，ASI\_UDTF.java已配置了将一行字符串按照竖线（\|）分割成多列字符串的代码。

    ```
    package ASI_UDTF;
    
    import org.apache.flink.api.java.tuple.Tuple2;
    import org.apache.flink.table.functions.TableFunction;
    
    public class ASI_UDTF extends TableFunction<Tuple2<String,String>> {
        public void eval(String str){
            String[] split = str.split("\\|");
            String name = split[0];
            String place = split[1];
            Tuple2<String,String> tuple2 = Tuple2.of(name,place);
            collect(tuple2);
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

    \\ASI\_UDX-main\\target\\目录下会出现ASI\_UDX-1.0-SNAPSHOT.jar的JAR包，即代表完成了UDTF开发工作。


## UDTF注册

UDTF注册过程，请参见[管理自定义函数（UDF）](/intl.zh-CN/Flink全托管/Flink SQL开发指南/管理自定义函数（UDF）.md)。

## UDTF使用

在注册UDTF完成后，您就可以使用UDTF，详细的操作步骤如下。

1.  Flink SQL作业开发。详情请参见[t1894286.md\#](/intl.zh-CN/Flink全托管/Flink SQL开发指南/作业开发.md)。

    ASI\_UDTF\_Source表中message字段每行字符串按照竖线（\|）分割成多列，代码示例如下。

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
    FROM ASI_UDTF_Source,lateral table(ASI_UDTF(`message`)) as T(name,place);
    ```

2.  在**作业列表**中，单击目标作业名称**操作**列的**启动**。

    启动成功后，ASI\_UDTF\_Sink表会被插入ASI\_UDTF\_Source表中message字段按照竖线（\|）分割成多列的字符。


