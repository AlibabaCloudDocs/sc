# 创建ElasticSearch（ES）结果表 {#concept_94716_zh .concept}

本文为您介绍如何创建实时计算ElasticSearch（ES）结果表。

**说明：** 本文档仅适用于独享模式。

## DDL定义 {#section_ls1_qhg_cgb .section}

ElasticSearch结果表的实现使用REST API，理论上兼容ElasticSearch的各个版本。实时计算支持使用ES作为结果输出。示例代码如下。

```language-sql
 CREATE TABLE es_stream_sink(
  field1 LONG, 
  field2 VARBINARY, 
  field3 VARCHAR,
  PRIMARY KEY(field1)
) WITH (
  type ='elasticsearch',
  endPoint = 'yourEndPoint',
  accessId = 'yourAccessId',
  accessKey = 'yourAccessSecret',
  index = 'yourIndex',
  typeName = 'yourTypeName'
);
```

**说明：** ES支持根据primary key进行update，primary key只能为1个字段。

-   指定primary key后，document的id为primaryKey字段的值。
-   未指定primary key的document对应的id为随机，详情请参见[Index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)。
-   full更新模式下，后面的doc会完全覆盖之前的doc，不会原地更新字段。
-   inc更新模式下，会依据传入的字段值更新对应的字段。
-   所有的更新默认为upsert语义，即insert or update。

## WITH参数 {#section_xgs_43g_cgb .section}

通用配置:

|参数|注释说明|默认值|Required|
|--|----|---|--------|
|endPoint|server地址，例：http://127.0.0.1:9211|无|是|
|accessId|访问实例ID|无|是|
|accessKey|访问实例密钥|无|是|
|index|索引名称，类似于数据库database的名称。|无|是|
|typeName|type 名称，类似于数据库的table名称。|无|是|
|bufferSize|流入多少条数据后开始去重|1000|否|
|maxRetryTimes|异常重试次数|30|否|
|timeout|读超时，单位为毫秒。|600000|否|
|discovery|是否开启节点发现。如果开启客户端会 5 分钟刷新一次 server list。|false|否|
|compression|是否使用 GZIP 压缩 request bodies|true|否|
|multiThread|是否开启 JestClient 多线程|true|否|
|ignoreWriteError|是否忽略写入异常|false|否|
|settings|创建index的settings配置|无|否|
|updateMode|指定 primary key 后的更新模式|full **说明：** 

-   full：全量覆盖
-   inc：增量更新

 |否|

## 动态索引相关 WITH 参数 {#section_pyd_qms_jgb .section}

|参数|注释说明|默认值|Required|
|dynamicIndex|是否开启动态索引|false\(true/false\)|否|
|indexField|抽取索引的字段名|/|dynamicIndex 为 true 时必填，只支持类型 Timestamp/Date/Long\(秒为单位的 timestamp\)|
|indexInterval|切换索引的周期|d|dynamicIndex 为 true时必填 -   d：天
-   m：月
-   w：周

 |

**说明：** 

-   仅实时计算2.2.7及以上版本支持动态索引功能。
-   当开启动态索引后， 基本配置中的`index`名称会作为后续创建索引的统一Alias，Alias和索引为一对多关系。
-   不同的`indexInterval`对应的真实索引名称：
    -   d -\> Alias + "yyyyMMdd"
    -   m -\> Alias + "yyyyMM"
    -   w -\> Alias + "yyyyMMW"
-   对于单个的真实索引可以使用 Index API修改，但是对于Alias只能`get`，若想更新Alias，请参见[Index Aliases](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)。

