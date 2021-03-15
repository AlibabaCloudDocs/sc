---
keyword: [消息队列Kafka, 源表]
---

# 创建消息队列Kafka源表

本文为您介绍如何创建实时计算Flink版消息队列Kafka源表、Kafka版本对应关系及Kafka消息解析示例。

**说明：**

-   本文仅适用于Blink 2.0及以上版本。
-   本文仅适用于独享模式。
-   Kafka源表支持读取自建Kafka集群，但需要注意版本对应关系，以及自建集群和实时计算Flink版集群的网络环境配置。
-   二进制数据不支持本地调试，语法检查没有问题请进行线上调试，详情请参见[线上调试](/intl.zh-CN/独享模式/Flink SQL开发指南/作业调试/线上调试.md)。

## 什么是Kafka源表

消息队列Kafka版是阿里云提供的分布式、高吞吐、可扩展的消息队列服务。消息队列Kafka版广泛用于日志收集、监控数据聚合、流式数据处理、在线和离线分析等大数据领域。实时计算Flink版支持将 Kafka作为流式数据的数据源表或结果表。

从Kafka输出的数据为序列化后的VARBINARY（二进制）格式。对于输出的每条数据，需要您编写自定义表值函数（UDTF）将其解析为序列化前的数据结构。Kafka源表数据解析流程通常为：Kafka Source Table -\> UDTF -\> Realtime Compute for Apache Flink -\> Sink。此外，Flink SQL中也支持通过CAST函数将VARBINARY解析为VARCHAR类型。自定义表值函数请参见[自定义表值函数（UDTF）](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/自定义表值函数（UDTF）.md)。

## DDL定义

Kafka源表定义DDL部分必须与以下SQL完全一致，可以更改WITH参数中的设置。

```
create table kafka_stream(   --必须和Kafka源表中的5个字段的顺序和类型保持一致。
  messageKey VARBINARY,
  `message`    VARBINARY,
  topic      VARCHAR,
  `partition`  INT,
  `offset`     BIGINT        
) with (
  type ='kafka010',
  topic = '<yourTopicName>',
  `group.id` = '<yourGroupId>',
  ...
);
```

## WITH参数

-   通用配置

    |参数|注释说明|是否必选|备注|
    |--|----|----|--|
    |type|Kafka对应版本|是|Kafka版本需要是Kafka08、Kafka09、Kafka010或Kafka011。版本对应关系请参见[Kafka版本对应关系](#section_o4c_b4z_bgb)。|
    |topic|读取的单个topic|是|无|
    |topicPattern|读取一批topic的表达式|否|Topic用竖线（\|）分割。例如，`topic1|topic2|topic3`。详情请参见[Class Pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html)。|
    |startupMode|启动位点|否 **说明：** 建议手动配置该参数。

|启动位点取值如下：     -   GROUP\_OFFSETS（默认值）：根据Group读取。
    -   EARLIEST：从Kafka最早分区开始读取。
    -   LATEST：从Kafka最新位点开始读取。
    -   TIMESTAMP：从指定的时间点读取。

您可以在WITH参数中指定startTimeMs（时间戳）或startTime参数（按照`yyyy-MM-dd HH:mm:ss`格式）。优先使用startTimeMs，不指定则按实时计算Flink版指定的启动位点时间开始消费。

**说明：**

    -   如果没有明文指定，则默认为GROUP\_OFFSETS模式。
    -   设置为TIMESTAMP模式时，需要在作业参数中明文指定时区。例如`blink.job.timeZone=Asia/Shanghai`。
    -   GROUP\_OFFSETS模式下，GroupID的第一次消费，没有设置偏移（Offset）值，默认从Kafka分区末尾开始读取数据。
    -   仅Kafka010和Kafka011版本支持TIMESTAMP。 |
    |partitionDiscoveryIntervalMS|定时检查是否有新分区产生|否|    -   Kafka 08版本：系统默认开启该功能。
    -   Kafka 09版本及以上版本：不支持partitionDiscoveryIntervalMS参数。您可以通过设置WITH参数，extraConfig=‘flink.partition-discovery.interval-millis=60000’达到相同的效果。
单位为毫秒，默认值为60000，即1分钟。 |
    |extraConfig|额外的KafkaConsumer配置项目|否|不在可选配置项中，但是期望额外增加的配置。例如：``fetch.message.max.bytes=104857600``，多个配置使用分号（;）分隔。|

-   Kafka08配置
    -   Kafka08必选配置

        |参数|注释说明|是否必选|
        |--|----|----|
        |group.id|消费组ID|是|
        |zookeeper.connect|zk链接地址|是|

    -   可选配置Key
        -   consumer.id
        -   socket.timeout.ms
        -   fetch.message.max.bytes
        -   num.consumer.fetchers
        -   auto.commit.enable
        -   auto.commit.interval.ms
        -   queued.max.message.chunks
        -   rebalance.max.retries
        -   fetch.min.bytes
        -   fetch.wait.max.ms
        -   rebalance.backoff.ms
        -   refresh.leader.backoff.ms
        -   auto.offset.reset
        -   consumer.timeout.ms
        -   exclude.internal.topics
        -   partition.assignment.strategy
        -   client.id
        -   zookeeper.session.timeout.ms
        -   zookeeper.connection.timeout.ms
        -   zookeeper.sync.time.ms
        -   offsets.storage
        -   offsets.channel.backoff.ms
        -   offsets.channel.socket.timeout.ms
        -   offsets.commit.max.retries
        -   dual.commit.enabled
        -   partition.assignment.strategy
        -   socket.receive.buffer.bytes
        -   fetch.min.bytes
-   Kafka09/Kafka010/Kafka011配置
    -   Kafka09/Kafka010/Kafka011必选配置

        |参数|注释说明|
        |--|----|
        |group.id|消费组ID|
        |bootstrap.servers|Kafka集群地址|

    -   Kafka09/Kafka010/Kafka011可选配置，请参见如下Kafka官方文档进行配置：

        -   [Kafka09](https://kafka.apache.org/090/documentation.html?spm=a2c4g.11186623.2.18.5fda779biQ4ToG#newconsumerconfigs)
        -   [Kafka010](https://kafka.apache.org/0100/documentation.html?spm=a2c4g.11186623.2.18.5fda779biQ4ToG#newconsumerconfigs)
        -   [Kafka011](https://kafka.apache.org/0110/documentation.html?spm=a2c4g.11186623.2.17.5fda779biQ4ToG#consumerconfigs)
        当需要配置某选项时，在DDL中的WITH部分增加对应的参数即可。例如，配置SASL登录，需增加``security.protocol``、``sasl.mechanism``和``sasl.jaas.config``3个参数，示例如下。

        ```
        create table kafka_stream(
          messageKey varbinary,
          `message` varbinary,
          topic varchar,
          `partition` int,
          `offset` bigint
        ) with (
          type ='kafka010',
          topic = '<yourTopicName>',
          `group.id` = '<yourGroupId>',
          ...,
          `security.protocol`='SASL_PLAINTEXT',
          `sasl.mechanism`='PLAIN',
          `sasl.jaas.config`='org.apache.kafka.common.security.plain.PlainLoginModule required username="<yourUserName>" password="<yourPassword>";'
        );
        ```


## Kafka版本对应关系

|type|Kafka版本|
|----|-------|
|Kafka08|0.8.22|
|Kafka09|0.9.0.1|
|Kafka010|0.10.2.1|
|Kafka011|0.11.0.2及以上|

## Kafka消息解析示例

-   场景1：将Kafka中的数据进行计算，并将计算结果输出到RDS。

    Kafka中保存了JSON格式数据，需要使用实时计算Flink版进行计算，消息格式示例如下。

    ```
    {
      "name":"Alice",
      "age":13,
      "grade":"A"
    }                
    ```

    -   方法1：Kafka SOURCE-\>Realtime Compute for Apache Flink-\>RDS SINK

        Blink 2.2.7及以上版本支持将VARBINARY类型通过CAST函数转换为VARCHAR类型，再通过JSON\_VALUE函数对Kafka数据进行解析，示例如下。

        ```
        CREATE TABLE kafka_src (
          messageKey  VARBINARY,
          `message`   VARBINARY,
          topic       VARCHAR,
          `partition` INT,
          `offset`    BIGINT
        ) WITH (
          type = 'kafka010'   --请参见Kafka版本对应关系。
        );
        
        CREATE TABLE rds_sink (
          `name`       VARCHAR,
          age         VARCHAR,
          grade       VARCHAR
        ) WITH(
          type='rds'
        );
        
        CREATE VIEW input_view AS 
          SELECT CAST(`message` as VARCHAR ) as `message`
        FROM kafka_src;
        
        INSERT INTO rds_sink
        SELECT 
          JSON_VALUE(`message`,'$.name'),
          JSON_VALUE(`message`,'$.age'),
          JSON_VALUE(`message`,'$.grade')
        FROM input_view;
        ```

    -   方法2：Kafka Source-\>UDTF-\>Realtime Compute for Apache Flink-\>RDS Sink

        针对不规则数据、复杂JSON数据，需要您自行编写UDTF代码进行解析，示例如下。

        -   SQL

            ```
            -- 定义解析Kafka message的UDTF。
            CREATE FUNCTION kafkaparser AS 'com.alibaba.kafkaUDTF';
            
            -- 定义源表。注意：Kafka源表DDL字段必须与以下示例完全一致。WITH中参数可以修改。
            CREATE TABLE kafka_src (
              messageKey  VARBINARY,
              `message`   VARBINARY,
              topic       VARCHAR,
              `partition` INT,
              `offset`    BIGINT
            ) WITH (
              type = 'kafka010',    --请参见Kafka版本对应关系。
              topic = 'test_kafka_topic',
              `group.id` = 'test_kafka_consumer_group',
              bootstrap.servers = 'ip1:port1,ip2:port2,ip3:port3'
            );
            
            CREATE TABLE rds_sink (
              name       VARCHAR,
              age        INT,
              grade      VARCHAR,
              updateTime TIMESTAMP
            ) WITH(
              type='rds',
              url='jdbc:mysql://localhost:3306/test',
              tableName='test4',
              userName='test',
              password='<yourDatabasePassword>'
            );
            
            -- 使用UDTF，将二进制数据解析成格式化数据。
            CREATE VIEW input_view (
              name,
              age,
              grade,
              updateTime
            ) AS
            SELECT
              T.name,
              T.age,
              T.grade,
              T.updateTime
            FROM
              kafka_src as S,
              LATERAL TABLE (kafkaparser (`message`)) as T (
              name,
              age,
              grade,
              updateTime
            );
            
            -- 使用解析出的格式化数据进行计算，并将结果输出到RDS。
            INSERT INTO rds_sink
            SELECT 
              name,
              age,
              grade,
              updateTime
            FROM input_view;                                
            ```

        -   UDTF

            UDTF创建步骤请参见[自定义表值函数（UDTF）](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/自定义表值函数（UDTF）.md)。Blink 2.2.4版本Maven依赖，示例如下。

            ```
                <dependencies>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-core</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-streaming-java_2.11</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-table_2.11</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>com.alibaba</groupId>
                        <artifactId>fastjson</artifactId>
                        <version>1.2.9</version>
                    </dependency>
                </dependencies>
            ```

            ```
            package com.alibaba;
            
            import com.alibaba.fastjson.JSONObject;
            import org.apache.flink.table.functions.TableFunction;
            import org.apache.flink.table.types.DataType;
            import org.apache.flink.table.types.DataTypes;
            import org.apache.flink.types.Row;
            import java.io.UnsupportedEncodingException;
            import java.sql.Timestamp;
            
            public class kafkaUDTF extends TableFunction<Row> {
                public void eval(byte[] message) {
                    try {
                        /* input message :
                            {
                              "name":"Alice",
                              "age":13,
                              "grade":"A",
                              "updateTime":1544173862
                            }
                        */
                        String msg = new String(message, "UTF-8");
                        try {
                            JSONObject data = JSON.parseObject(msg);
                            if (data != null) {
                                String name = data.getString("name") == null ? "null" : data.getString("name");
                                Integer age = data.getInteger("age") == null ? 0 : data.getInteger("age");
                                String grade = data.getString("grade") == null ? "null" : data.getString("grade");
                                Timestamp updateTime = data.getTimestamp("updateTime");
            
                                Row row = new Row(4);
                                row.setField(0, name);
                                row.setField(1, age);
                                row.setField(2, grade);
                                row.setField(3,updateTime );
            
                                System.out.println("Kafka message str ==>" + row.toString());
                                collect(row);
                            }
                        } catch (ClassCastException e) {
                            System.out.println("Input data format error. Input data " + msg + "is not json string");
                        }
                    } catch (UnsupportedEncodingException e) {
                        e.printStackTrace();
                    }
                }
                @Override
                // 如果返回值是Row，重新加载UDTF这个方法，并指明系统返回的字段类型。
                public DataType getResultType(Object[] arguments, Class[] argTypes) {
                    return DataTypes.createRowType(DataTypes.STRING, DataTypes.INT, DataTypes.STRING, DataTypes.TIMESTAMP);
                }
            }                                
            ```

-   场景2：从Kafka读取数据，输入实时计算Flink版进行窗口计算。

    按照实时计算Flink版目前的设计，滚动或滑动等窗口操作，必须在源表DDL上定义[Watermark](/intl.zh-CN/独享模式/Flink SQL参考/基本概念/Watermark.md)。Kafka源表比较特殊。如果要以Kafka中message字段中的Event Time进行窗口操作，需要先从message字段使用UDX解析出Event Time，才能定义Watermark。在Kafka源表场景中，需要使用[计算列](/intl.zh-CN/独享模式/Flink SQL参考/基本概念/计算列.md)。例如Kafka中写入数据：`2018-11-11 00:00:00|1|Anna|female` 。计算流程为：Kafka Source-\>UDTF-\>Realtime Compute for Apache Flink-\>RDS Sink。

    -   方法1：Kafka SOURCE-\>Realtime Compute for Apache Flink-\>RDS SINK

        Blink 2.2.7及以上版本支持将VARBINARY类型通过CAST函数转换为VARCHAR类型，再通过JSON\_VALUE函数对Kafka数据进行解析，示例如下。

        ```
        CREATE TABLE kafka_src (
          messageKey VARBINARY,
          `message` VARBINARY,
          topic VARCHAR,
          `partition` INT,
          `offset` BIGINT,
          ts as to_timestamp(json_value(cast(`message` as VARCHAR ),'$.nodes.time')),
          WATERMARK wk FOR ts as withOffset(ts, 2000)
        ) WITH (type = 'kafka' --请参见Kafka版本对应关系。
        );
        
        CREATE TABLE rds_sink (
          starttime TIMESTAMP ,
          endtime   TIMESTAMP ,
          `message` BIGINT 
        ) WITH (type = 'rds');
        
        INSERT
          INTO rds_sink
        SELECT
          TUMBLE_START(ts, INTERVAL '1' MINUTE),
          TUMBLE_END(ts, INTERVAL '1' MINUTE),
          count(`message`)
        FROM
          kafka_src
        GROUP BY TUMBLE(ts, INTERVAL '1' MINUTE);
        ```

    -   方法2：Kafka SOURCE-\>UDTF-\>Realtime Compute for Apache Flink-\>RDS SINK
        -   SQL

            ```
            -- 定义解析Kafka message的UDTF。
            CREATE FUNCTION kafkapaser AS 'com.alibaba.kafkaUDTF';
            CREATE FUNCTION kafkaUDF AS 'com.alibaba.kafkaUDF';
            
            -- 定义源表，注意：Kafka源表DDL字段必须与以下示例一模一样。WITH中参数可改。
            create table kafka_src (
              messageKey VARBINARY,
              `message` VARBINARY,
              topic VARCHAR,
              `partition` INT,
              `offset` BIGINT,
              ctime AS TO_TIMESTAMP(kafkaUDF(`message`)), -- 定义计算列，计算列可理解为占位符，源表中并没有这一列，其中的数据可经过下游计算得出。注意:计算列的类型必须为TIMESTAMP才能创建Watermark。
              watermark for `ctime` as withoffset(`ctime`,0) -- 在计算列上定义Watermark。
            ) WITH (
              type = 'kafka010', -- 请参见Kafka版本对应关系。
              topic = 'test_kafka_topic',
              `group.id` = 'test_kafka_consumer_group',
              bootstrap.servers = 'ip1:port1,ip2:port2,ip3:port3'
            );
            
            create table rds_sink (
              `name` VARCHAR,
              age INT,
              grade VARCHAR,
              updateTime TIMESTAMP
            ) WITH(
              type='rds',
              url='jdbc:mysql://localhost:3306/test',
              tableName='test4',
              userName='test',
              password='<yourPassword>'
            );
            
            
            -- 使用UDTF，将二进制数据解析成格式化数据。
            CREATE VIEW input_view AS
            SELECT
              S.ctime,
              T.`order`,
              T.`name`,
              T.sex
              from
              kafka_src as S,
              LATERAL TABLE (kafkapaser (`message`)) as T (
              ctime,
              `order`,
              `name`,
              sex
            );
            
            -- 对input_view中输出的数据做计算。
            CREATE VIEW view2 (
              cnt,
              sex
            ) AS
              SELECT
              COUNT(*) as cnt,
              T.sex
              from
              input_view
            Group BY sex, TUMBLE(ctime, INTERVAL '1' MINUTE);
            
            -- 使用解析出的格式化数据进行计算，并将结果输出到RDS。
            insert into rds_sink
              SELECT
              cnt,sex
            from view2;
            ```

        -   UDF&UDTF

            UDF和UDTF创建步骤请参见[自定义标量函数（UDF）](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/自定义标量函数（UDF）.md)和[自定义表值函数（UDTF）](/intl.zh-CN/独享模式/Flink SQL参考/自定义函数（UDX）/自定义表值函数（UDTF）.md)。Blink 2.2.4版本Maven依赖，示例如下。

            ```
              <dependencies>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-core</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-streaming-java_2.11</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-table_2.11</artifactId>
                        <version>blink-2.2.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>com.alibaba</groupId>
                        <artifactId>fastjson</artifactId>
                        <version>1.2.9</version>
                    </dependency>
                </dependencies>                                
            ```

            -   UDTF

                ```
                package com.alibaba;
                
                import com.alibaba.fastjson.JSONObject;
                import org.apache.flink.table.functions.TableFunction;
                import org.apache.flink.table.types.DataType;
                import org.apache.flink.table.types.DataTypes;
                import org.apache.flink.types.Row;
                import java.io.UnsupportedEncodingException;
                
                /**
                  以下例子解析输入Kafka中的JSON字符串，并将其格式化输出。
                **/
                public class kafkaUDTF extends TableFunction<Row> {
                
                    public void eval(byte[] message) {
                        try {
                          // 读入一个二进制数据，并将其转换为String格式。
                            String msg = new String(message, "UTF-8");
                
                                // 提取JSON Object中各字段。
                                    String ctime = Timestamp.valueOf(data.split('\\|')[0]);
                                    String order = data.split('\\|')[1];
                                    String name = data.split('\\|')[2];
                                    String sex = data.split('\\|')[3];
                
                                    // 将解析出的字段放到要输出的Row()对象。
                                    Row row = new Row(4);
                                    row.setField(0, ctime);
                                    row.setField(1, age);
                                    row.setField(2, grade);
                                    row.setField(3, updateTime);
                
                                    System.out.println("Kafka message str ==>" + row.toString());
                
                                    // 输出一行。
                                    collect(row);
                
                            } catch (ClassCastException e) {
                                System.out.println("Input data format error. Input data " + msg + "is not json string");
                            }
                
                
                        } catch (UnsupportedEncodingException e) {
                            e.printStackTrace();
                        }
                
                    }
                
                    @Override
                    // 如果返回值是Row，重新加载UDTF这个方法，并指明系统返回的字段类型。
                    // 定义输出Row()对象的字段类型。
                    public DataType getResultType(Object[] arguments, Class[] argTypes) {
                        return DataTypes.createRowType(DataTypes.TIMESTAMP,DataTypes.STRING, DataTypes.Integer, DataTypes.STRING,DataTypes.STRING);
                    }
                
                }
                ```

            -   UDF

                ```
                package com.alibaba;
                package com.hjc.test.blink.sql.udx;
                import org.apache.flink.table.functions.FunctionContext;
                import org.apache.flink.table.functions.ScalarFunction;
                
                public class KafkaUDF extends ScalarFunction {
                    // 可选，open方法可以不写。
                    // 需要import org.apache.flink.table.functions.FunctionContext;
                
                    public String eval(byte[] message) {
                
                         // 读入一个二进制数据，并将其转换为String格式。
                        String msg = new String(message, "UTF-8");
                        return msg.split('\\|')[0];
                    }
                    public long eval(String b, String c) {
                        return eval(b) + eval(c);
                    }
                    //可选，close方法可以不写。
                    @Override
                    public void close() {
                        }
                }                                        
                ```


## 自建Kafka

-   示例

    ```
    create table kafka_stream(
      messageKey VARBINARY,
      `message` VARBINARY, 
      topic varchar,
      `partition` int,
      `offset` bigint
    ) with (
      type ='kafka011',
      topic = 'kafka_01',
      `group.id` = 'CID_blink',
      bootstrap.servers = '192.168.0.251:****'
    );                
    ```

-   WITH参数

    详情请参见[WITH参数](#section_ivk_14z_bgb)。

    **说明：**

    -   bootstrap.servers参数需要填写自建的地址和端口号。
    -   仅在Blink 2.2.6及以上版本支持阿里云Kafka或自建Kafka显示TPS和RPS等指标信息。

## 常见问题

-   Q：作业启动时产生如下报错，应该如何处理？

    ```
    ERR_ID:
         SQL-00010007
    CAUSE:
         Could not create table 'kafka_source' as source table
    ACTION:
         Please refer to details section for hint.
         If it doesn't help, please contact customer support
    DETAIL:
         java.lang.IllegalArgumentException: Startup time[1566481803000] must be before current time[1566453003356].
    ```

    A：时区设置错误导致以上报错。请在作业参数中增加如下参数。

    ```
    blink.job.timeZone=Asia/Shanghai
    ```

-   Q：自建Kafka消费不到数据，应该如何排查？

    A：

    -   问题原因

        Kafka各broker会向zookeeper汇报自身Meta信息，Kafka consumer最终会根据broker Meta信息listener\_security\_protocol中的Endpoint地址（例如IP或本机域名加端口）访问broker拉取数据。如果实时计算Flink版作业所在机器无法访问Endpoint地址，则connector中consumer将无法拉取数据，导致流程的停滞。

    -   排查思路
        1.  查看**zookeeper** \> **broker** \> **listener\_security\_protocol** \> **Endpoint**信息。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9084359951/p64421.jpg)

        2.  通过**网络探测**功能检测Endpoint的IP或域名是否可访问。
        3.  登录机器再次确认。
    -   解决方案
        -   如果Endpoint采用的是IP形式，则确认Kafka服务端是否配置了[数据存储白名单配置](/intl.zh-CN/独享模式/Flink SQL开发指南/数据存储/数据存储白名单配置.md)。如果未配置，请配置后重试。
        -   如果Endpoint采用的是域名形式，由于实时计算Flink版独享集群内部无法解析域名，所以需要在白名单已配置的情况下，通过以下两种解方案解决：
            -   不能重启Kafka服务

                购买[云解析PrivateZone](https://www.aliyun.com/product/pvtz?spm=5176.10695662.1395782.1.305e10614trdiP)，配置所有Kafka Broker的域名解析，通过域名进行网络探测成功后，重启实时计算Flink版作业。

            -   可以重启Kafka服务

                将Kafka的advertised.listeners属性配置为相应Broker的IP和端口（通常为boostrap.servers中IP和Port），以保证网络畅通，然后重启实时计算Flink版作业。

-   Q：消费Kafka时，为什么作业一启动后马上就结束了？

    A：Blink 3.3.0以下版本的Kafka Connector，使用timestamp模式时。如果设置启动位点后，Kafka的所有partition均没数据，则Connector会判定没有partition消费而结束作业。通常对应的`TaskManager.log`日志中会存在类似`Consumer subtask {} initially has no partitions to read from.`的日志信息。建议升级至Blink 3.3.0及以上版本。

-   Q：实时计算Flink版中的COMMIT OFFSET有什么作用？

    A：实时计算Flink版默认采用commitOffsetOnCheckpointing，用户设置的Commit Offset策略不生效。只有开启checkpoint后，在每次checkpoint成功时，才会commit当前分区消费的offset至Kafka，以便在作业失败恢复过程中，从上一次checkpoint的commit位点开始消费，保证计算的Exactly Once。如果将checkpoint间隔设置过大，Kafka端可能会查询不到当前消费offset。


