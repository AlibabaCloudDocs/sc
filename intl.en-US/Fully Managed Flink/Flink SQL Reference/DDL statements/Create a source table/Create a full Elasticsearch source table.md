---
keyword: [Elasticsearch, ES, source table]
---

# Create a full Elasticsearch source table

This topic describes how to create a full Elasticsearch source table. It also describes data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a full Elasticsearch source table.

**Note:**

-   You can create full Elasticsearch source tables only when Elasticsearch V5.5 or later is used.
-   You are allowed to create only full Elasticsearch source tables. Incremental Elasticsearch source tables cannot be created.
-   The fields in the DDL statement correspond to the fields in an Elasticsearch document. Document IDs cannot be written to the source table.
-   The Elasticsearch connector can be used to store source tables of streaming jobs and batch jobs.

## Introduction to Elasticsearch

[Alibaba Cloud Elasticsearch](/intl.en-US/Product Introduction/What is Alibaba Cloud Elasticsearch?.md) is compatible with open-source Elasticsearch features, such as Security, Machine Learning, Graph, and Application Performance Management \(APM\). Alibaba Cloud Elasticsearch also provides enterprise-level services, including access control, security monitoring and alerting, and automatic generation of reports.

## Prerequisites

An Elasticsearch index is created. For more information, see [Step 1: Create a cluster](/intl.en-US/Elasticsearch Instances Management/Quick start.md).

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
|endPoint|The endpoint of the Elasticsearch server.|Yes|Example: http://127.0.0.1:9200.|
|accessId|The username that is used to access the Elasticsearch cluster.|No|None.|
|accessKey|The password that is used to access the Elasticsearch cluster.|No|None.|
|indexName|The name of the index.|Yes|None.|
|typeNames|The name of the type.|No|Default value: `_doc`. **Note:** We recommend that you do not set this parameter in versions later than Elasticsearch V7.0. |
|batchSize|The maximum number of documents that can be obtained from the Elasticsearch cluster for each scroll request.|No|Default value: 2000.|
|keepScrollAliveSecs|The maximum retention period of the scroll context.|No|Unit: seconds. Default value: 3600.|

## Mapping between field data types

Flink parses Elasticsearch data in the JavaScript Object Notation \(JSON\) format. For more information, see [Data Type Mapping](https://ci.apache.org/projects/flink/flink-docs-master/docs/connectors/table/formats/json/)

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

