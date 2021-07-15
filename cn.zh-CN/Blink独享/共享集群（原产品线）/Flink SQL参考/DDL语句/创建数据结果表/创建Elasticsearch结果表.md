# 创建Elasticsearch结果表

本文为您介绍如何创建实时计算Flink版Elasticsearch（ES）结果表以及创建结果表时使用的WITH参数。

**说明：** 本文仅适用于Blink 3.2.2及以上版本。

## DDL定义

实时计算Flink版支持使用ES作为结果输出，示例代码如下。

```
 CREATE TABLE es_stream_sink(
  field1 LONG,
  field2 VARBINARY,
  field3 VARCHAR,
  PRIMARY KEY(field1)
)WITH(
  type ='elasticsearch',
  endPoint = 'http://es-cn-mp****.public.elasticsearch.aliyuncs.com:****',
  accessId = '<yourAccessId>',
  accessKey = '<yourAccessSecret>',
  index = '<yourIndex>',
  typeName = '<yourTypeName>'
);
```

**说明：**

-   ES支持根据PRIMARY KEY进行UPDATE，且PRIMARY KEY只能为1个字段。
-   在指定PRIMARY KEY后，Document的ID为PRIMARY KEY字段的值。
-   在未指定PRIMARY KEY时，Document的ID为随机，详情请参见[Index API](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)。
-   在full更新模式下，新增的doc会完全覆盖已存在的doc。
-   在inc更新模式下，系统会依据输入的字段值更新对应的字段。
-   所有的更新默认为UPSERT语义，即INSERT或UPDATE。

## WITH参数（通用配置）

|参数|说明|是否必选|默认值|
|--|--|----|---|
|type|connector类型。|是|elasticsearch|
|endPoint|Server地址，例入：http://127.0.0.1:9211。|是|无|
|accessId|创建ES时的登录名。**说明：** 如果您通过Kibana插件操作ES，请填写Kibana登录ID。

|是|无|
|accessKey|创建ES时的登录密码 。**说明：** 如果您通过Kibana插件操作ES，请填写Kibana登录密码。

|是|无|
|index|索引名称。|是|无|
|typeName|文档类型。|是|`_doc`|
|bufferSize|流入多少条数据后开始去重。|否|1000|
|maxRetryTimes|异常重试次数。|否|30|
|timeout|读超时，单位为毫秒。|否|600000|
|discovery|是否开启节点发现。如果开启，客户端每5分钟刷新一次Server List。|否|false|
|compression|是否使用GZIP压缩Request Bodies。|否|true|
|multiThread|是否开启JestClient多线程。|否|true|
|ignoreWriteError|是否忽略写入异常。|否|false|
|settings|创建Index的Settings配置。|否|无|
|updateMode|指定主键（PRIMARY KEY）后的更新模式：-   full：全量覆盖。
-   inc：增量更新。

|否|full|

## WITH参数（动态索引相关）

|参数|说明|是否必选|备注|
|--|--|----|--|
|dynamicIndex|是否开启动态索引。|否|参数取值如下：-   true：开启。
-   false（默认值）：不开启。 |
|indexField|抽取索引的字段名。|dynamicIndex为true时必填。|只支持TIMESTAMP（以秒为单位）、DATE和LONG3种数据类型。|
|indexInterval|切换索引的周期。|dynamicIndex为true时必填。|参数值如下： -   d（默认值）：天
-   m：月
-   w：周 |
|mapping|启用动态索引时，设置文档各字段的类型与格式。例如，设置名为sendTime字段的格式：```
{
 "properties": {    
 "sendTime": {     
 "type":   "date",     
 "format": "yyyy-MM-dd HH:mm:ss"    
    }
  }
}
```

|否|默认值为空。**说明：**

-   Blink 3.7.0版本及以上版本支持该参数。
-   Elasticsearch 7.0不支持mapping参数。 |

**说明：**

-   仅Blink 2.2.7及以上版本支持动态索引功能。
-   当开启动态索引后，基本配置中的`index`名称会作为后续创建索引的统一Alias，Alias和索引为一对多关系。
-   不同的`indexInterval`对应的真实索引名称：
    -   d -\> Alias "yyyyMMdd"
    -   m -\> Alias "yyyyMM"
    -   w -\> Alias "yyyyMMW"
-   对于单个的真实索引可使用Index API进行修改，但对于Alias只支持`get`功能。如果需要更新Alias，请参见[Index Aliases](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)。

