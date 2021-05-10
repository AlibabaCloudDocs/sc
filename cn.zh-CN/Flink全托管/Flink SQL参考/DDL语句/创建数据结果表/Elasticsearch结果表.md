---
keyword: [Elasticsearch, ES, 结果表]
---

# Elasticsearch结果表

本文为您介绍Elasticsearch（ES）结果表DDL定义、WITH参数、类型映射和代码示例。

**说明：** 该Connector支持的功能是和阿里云Elasticsearch产品对齐的，支持HTTPS加密访问形式。

## 什么是云数据库Elasticsearch

[阿里云Elasticsearch](/cn.zh-CN/产品简介/什么是阿里云Elasticsearch.md)兼容开源Elasticsearch的功能，以及Security、Machine Learning、Graph、APM等商业功能，致力于数据分析、数据搜索等场景服务。为您提供企业级权限管控、安全监控告警、自动报表生成等场景服务。

## DDL定义

```
 CREATE TABLE es_sink(
   user_id   STRING,
   user_name   STRING,
   uv BIGINT,
   pv BIGINT,
   PRIMARY KEY (user_id) NOT ENFORCED  -- 主键可选，如果定义了主键，则作为文档ID，否则文档ID将为随机值。
) WITH (
   'connector' = 'elasticsearch-6',
   'hosts' = '<yourHosts>',
   'index' = '<yourIndex>',
   'document-type' = '<yourEelasticsearch.types>',
   'username' ='<yourElasticsearch.accessId>',
   'password' ='<yourElasticsearch.accessKey>'
);
```

**说明：**

-   仅支持Elasticsearch 6.x和7.x版本。
-   DDL中的字段均对应Elasticsearch文档中的字段，不支持将文档ID写入表中。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为`elasticsearch-6`或`elasticsearch-7`。|
|hosts|Server地址|是|例如：127.0.0.1:9200。|
|index|文档索引名称|是|默认为空，不进行权限验证。|
|document-type|Type名称|-   `elasticsearch-6`：必填
-   `elasticsearch-7`：不支持

|无|
|username|访问实例ID|否|默认为空，不进行权限验证。|
|password|访问实例密钥|否|如果定义了username，则必须定义非空的password。|
|document-id.key-delimiter|文档ID的分隔符|否|默认值为`_`。|
|failure-handler|Elasticsearch请求失败时的故障处理策略。|否|可选策略如下：-   fail（默认值）：如果请求失败，则作业失败。
-   ignore：忽略失败并删除请求。
-   retry\_rejected：重新添加由于队列容量满而失败的请求。
-   custom class name：用于使用ActionRequestFailureHandler子类进行故障处理。 |
|sink.flush-on-checkpoint|是否在checkpoint时执行flush。|否|默认值为true。禁用该功能后，在Elasticsearch进行Checkpoint时，connector将不等待确认所有pending请求已完成。因此，connector不会为请求提供at-least-once保证。|
|sink.bulk-flush.backoff.strategy|如果由于临时请求错误导致flush操作失败，则设置sink.bulk-flush.backoff.strategy指定重试策略。|否|-   DISABLED（默认值）：不执行重试，即第一次请求错误后失败。
-   CONSTANT：常量回退，即每次回退等待时间相同。
-   EXPONENTIAL：指数回退，即每次回退等待时间指数递增。 |
|sink.bulk-flush.backoff.max-retries|最大回退重试次数。|否|默认值为8。|
|sink.bulk-flush.backoff.delay|每次回退尝试之间的延迟。|否|-   CONSTANT：每次重试之间的延迟。
-   EXPONENTIAL：这是初始基准延迟。 |
|sink.bulk-flush.max-actions|每个批量请求的最大缓冲操作数。|否|默认值为1000，0表示禁用该功能。|
|sink.bulk-flush.max-size|存放请求的缓冲区内存最大值。|否|单位为MB，默认值为2，0表示禁用该功能。|
|sink.bulk-flush.interval|flush的间隔。|否|默认值为1s，0表示禁用该功能。|
|connection.path-prefix|要添加到每个REST通信中的前缀字符串。|否|默认值为空。|

## 文档ID

Elasticsearch Sink可以根据是否定义了主键确定是其在upsert模式还是在append模式下工作。如果定义了主键，Elasticsearch Sink将在upsert模式下工作，该模式可以消费包含UPDATE和DELETE的消息。如果未定义主键，Elasticsearch Sink将以append模式工作，该模式只能消费INSERT消息。

在Elasticsearch Sink中，主键用于计算Elasticsearch的文档ID。文档ID为最多512个字节不包含空格的字符串。Elasticsearch Sink通过使用document-id.key-delimiter指定的键分隔符按照DDL中定义的顺序连接所有主键字段，从而为每一行生成一个文档ID字符串。某些类型（例如BYTES、ROW、ARRAY和MAP等）由于没有对应的字符串表示形式，所以不允许其作为主键字段。如果未指定主键，Elasticsearch将自动生成随机的文档ID。

## 动态索引

Elasticsearch Sink同时支持静态索引和动态索引：

-   如果使用静态索引，则索引选项值应为纯字符串，例如myusers，所有记录都将被写入myusers索引。
-   如果使用动态索引，可以使用`{field_name}`引用记录中的字段值以动态生成目标索引。您还可以使用 \{field\_name\|date\_format\_string\}将TIMESTAMP、DATE和TIME类型的字段值转换为date\_format\_string指定的格式。date\_format\_string与Java的DateTimeFormatter兼容。例如，如果设置为myusers-\{log\_ts\|yyyy-MM-dd\}，则log\_ts字段值为2020-03-27 12:25:55的记录将被写入myusers-2020-03-27索引。

## 类型映射

Flink以JSON来解析Elasticsearch数据，详情请参见[数据类型映射关系](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/formats/json.html)。

## 代码示例

```
CREATE TEMPORARY TABLE datagen_source (
  id STRING, 
  name STRING,
  uv BIGINT
) with (
  'connector' = 'datagen'
);

CREATE TEMPORARY TABLE es_sink (
   user_id STRING,
   user_name STRING,
   uv BIGINT,
   PRIMARY KEY (user_id) NOT ENFORCED -- 主键可选，如果定义了主键，则作为文档ID，否则文档ID将为随机值。
) WITH (
   'connector' = 'elasticsearch-7',
   'hosts' = '<yourHosts>',
   'index' = '<yourIndex>',
   'document-type' = '<yourElasticsearch.types>',
   'username' ='<yourElasticsearch.accessId>',
   'password' ='<yourElasticsearch.accessKey>'
);

INSERT INTO es_sink
   SELECT id, name, uv
FROM datagen_source;
```

