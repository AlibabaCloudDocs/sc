# 创建ElasticSearch维表 {#concept_878569 .concept}

本文为您介绍如何创建实时计算ElasticSearch（ES）维表。

**说明：** 本文仅适用于实时计算3.2.2及以上版本。

## DDL定义 {#section_pf4_u42_xtx .section}

实时计算支持使用ElasticSearch（ES）作为维表，示例代码如下。
做维表有误
``` {#codeblock_3ix_9ik_21x .language-sql}
 CREATE TABLE es_stream_sink(
  field1 LONG, 
  field2 VARBINARY, 
  field3 VARCHAR,
  PRIMARY KEY(field1),
  PERIOD FOR SYSTEM_TIME
) WIHT (
  type ='elasticsearch',
  endPoint = '<yourEndPoint>',
  accessId = '<yourAccessId>',
  accessKey = '<yourAccessSecret>',
  index = '<yourIndex>',
  typeName = '<yourTypeName>'
);
```

**说明：** ES支持根据PRIMARY KEY进行UPDATE，且PRIMARY KEY只能为1个字段。

## WITH参数 {#section_pqg_3fy_9c9 .section}

通用配置:

|参数|注释说明|默认值|Required|
|--|----|---|--------|
|endPoint|Server地址，例如：http://127.0.0.1:9211。|无|是|
|accessId|访问实例ID。|无|是|
|accessKey|访问实例密钥。|无|是|
|index|索引名称，类似于数据库Database的名称。|无|是|
|typeName|Type名称，类似于数据库的Table名称。|无|是|
|maxRetryTimes|异常重试次数。|30|否|
|timeout|读取超时时长，单位为毫秒。|600000|否|
|discovery|是否开启节点发现。如果开启，客户端每5分钟刷新一次Server List。|false|否|
|compression|是否使用GZIP压缩Request Bodies。|true|否|
|multiThread|是否开启JestClient多线程。|true|否|

## CACHE参数 {#section_vdu_zae_apq .section}

|参数|注释说明|备注|
|cache|缓存策略|默认 `None`, 可选 `LRU`, `ALL`。 -   None：无缓存。
-   LRU：最近使用策略缓存。需要配置cacheSize参数和cacheTTLMs参数。
-   ALL：全量缓存策略。

 |
|cacheSize|缓存大小|选择`LRU`缓存策略后，可以设置缓存大小，默认为10000行。|
|cacheTTLMs|缓存超时时长|默认缓存不超时，单位为毫秒。不同缓存策略下的功能如下： -   LRU：设置缓存失效的超时时长。
-   ALL：设置缓存Reload的间隔时长，默认不重新加载。

 |

