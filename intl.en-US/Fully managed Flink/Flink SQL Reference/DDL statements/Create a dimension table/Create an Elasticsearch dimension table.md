---
keyword: [Elasticsearch, ES, dimension table]
---

# Create an Elasticsearch dimension table

This topic describes how to create an Elasticsearch dimension table. It also describes the data definition language \(DDL\) statements, parameters in the WITH and CACHE clauses, and sample code used when you create an Elasticsearch dimension table.

**Note:** The features supported by the Elasticsearch connector are consistent with Alibaba Cloud Elasticsearch. This connector does not support HTTPS-based access.

## DDL syntax

```
 CREATE TABLE es_dim(
   field1 LONG, --- If this field is used as a key to join a dimension table with another table, the value of this field must be of the STRING type.
   field2 VARBINARY, 
   field3 VARCHAR
) WITH (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);
```

**Note:**

-   Only one key can be used to join a dimension table with another table. The key is the document ID of the index for Elasticsearch.
-   Elasticsearch V5.5 and later are supported.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the dimension table.|Yes|Set the value to `elasticsearch`.|
|endPoint|The endpoint of the Elasticsearch cluster.|Yes|Example: http://127.0.0.1:9200.|
|accessId|The AccessKey ID that is used to access the database.|No|None.|
|accessKey|The AccessKey secret that is used to access the database.|No|None.|
|indexName|The document index name.|Yes|None.|
|typeNames|The type of the document.|No|Default value: `_doc`.**Note:** We recommend that you do not specify this parameter if you use a version of Elasticsearch later than V7.0. |

## Parameters in the CACHE clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|cache|The cache policy.|No|Valid values for an Elasticsearch dimension table:-   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, the system searches for the data record in the physical dimension table.

If you use this cache policy, you must configure the cacheSize and cacheTTLMs parameters. |
|cacheSize|The maximum number of data records that can be cached.|No|This parameter is available only if you set the cache parameter to `LRU`. Default value: 10000.|
|cacheTTLMs|The interval at which the cache is refreshed.|No|The cache does not expire by default. Unit: milliseconds. The purpose of setting this parameter varies based on the cache policy. -   If you set the cache parameter to LRU, the cacheTTLMs parameter specifies the time before cache entries expire.
-   If you set the cache parameter to ALL, the cacheTTLMs parameter specifies the interval at which the system refreshes the cache. By default, the cache is not refreshed. |
|cacheEmpty|Specifies whether to cache empty results.|No|Default value: true.|

## Field type mapping

Flink parses Elasticsearch data in the JSON format. For more information, see [Field type mapping](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/formats/json.html).

## Sample code

```
CREATE TEMPORARY TABLE datagen_source (
   id STRING, 
   data STRING,
   proctime as PROCTIME()
)  with (
   'connector' = 'datagen' 
);

CREATE TEMPORARY TABLE es_dim (
   id STRING,
   `value` FLOAT
) WITH (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);

CREATE TEMPORARY TABLE blackhole_sink (
  id STRING,
  data STRING,
  `value` FLOAT
) WITH (
  'connector' = 'blackhole' 
);

INSERT INTO blackhole_sink
SELECT e.*, w. *
  FROM datagen_source AS e
JOIN es_dim FOR SYSTEM_TIME AS OF e.proctime AS w
ON e.id = w.id;
```

