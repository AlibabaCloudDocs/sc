---
keyword: [Elasticsearch, ES, source table]
---

# Create a full Elasticsearch source table

This topic describes how to create a full Elasticsearch source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a full Elasticsearch source table.

**Note:**

-   Only Elasticsearch V5.5 and later are supported.
-   Only full Elasticsearch source tables are supported. Incremental Elasticsearch source tables are not supported.
-   The fields in the DDL statement correspond to the fields in an Elasticsearch document. Document IDs cannot be written to the source table.

## DDL syntax

```
 CREATE TABLE elasticsearch_source(
   name STRING, 
   location STRING, 
   `value` FLOAT
) WITH (
   'connector' ='elasticsearch',
   'endPoint' = '<yourEndPoint>',
   'accessId' = '<yourAccessId>',
   'accessKey' = '<yourAccessSecret>',
   'indexName' = '<yourIndexName>',
   'typeNames' = '<yourTypeName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the source table.|Yes|Set the value to `elasticsearch`.|
|endPoint|The endpoint of the Elasticsearch cluster.|Yes|Example: http:// 127.0.0.1:9200.|
|accessId|The AccessKey ID that is used to access the database.|No|None.|
|accessKey|The AccessKey secret that is used to access the database.|No|None.|
|indexName|The document index name.|Yes|None.|
|typeNames|The document type.|No|Default value: `_doc`.**Note:** We recommend that you do not set this parameter in versions later than Elasticsearch V7.0. |
|batchSize|The maximum number of documents that can be obtained from the Elasticsearch cluster for each scroll request.|No|Default value: 2000.|
|keepScrollAliveSecs|The maximum retention period of the scroll context.|No|Unit: seconds. Default value: 3600.|

## Field type mapping

Flink parses Elasticsearch data in the JSON format. For more information, see [Data type mapping](https://ci.apache.org/projects/flink/flink-docs-master/zh/dev/table/connectors/formats/json.html).

## Sample code

```
CREATE TEMPORARY TABLE elasticsearch_source (
   name STRING,
   location STRING,
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
   name STRING,
   location STRING,
   `value` FLOAT
) WITH (
   'connector' ='blackhole'
);

INSERT INTO blackhole_sink
SELECT name, location, `value`
FROM elasticsearch_source;
```

