---
keyword: [Elasticsearch, ES, result table]
---

# Create an Elasticsearch result table

This topic describes how to create an Elasticsearch result table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create an Elasticsearch result table.

**Note:** The features supported by Elasticsearch result tables are consistent with Alibaba Cloud Elasticsearch. Elasticsearch result tables cannot be accessed over HTTPS.

## DDL syntax

```
 CREATE TABLE es_sink(
   user_id   STRING,
   user_name   STRING,
   uv BIGINT,
   pv BIGINT,
   PRIMARY KEY (user_id) NOT ENFORCED  -- The primary key is optional. If you specify a primary key, it is used as the document ID. If you do not specify a primary key, the document ID is a random value.
) WITH (
   'connector' = 'elasticsearch-7',
   'hosts' = '<yourHosts>',
   'index' = '<yourIndex>',
   'document-type' = '<yourEelasticsearch.types>',
   'username' ='<yourElasticsearch.accessId>',
   'password' ='<yourElasticsearch.accessKey>'
);
```

**Note:**

-   Only Elasticsearch V6.0 and Elasticsearch V7.0 are supported.
-   The fields in the DDL statement correspond to the fields in an Elasticsearch document. Document IDs cannot be written to the result table.

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to `elasticsearch-6` or `elasticsearch-7`.|
|hosts|The endpoint of the Elasticsearch cluster.|Yes|Example: 127.0.0.1:9200.|
|index|The document index name.|Yes|This parameter is empty by default. No permission verification is required.|
|document-type|The type of the document.|-   `elasticsearch-6`: required
-   `elasticsearch-7`: not supported

|None.|
|username|The AccessKey ID that is used to access the Elasticsearch cluster.|No|This parameter is empty by default. No permission verification is required.|
|password|The AccessKey secret that is used to access the Elasticsearch cluster.|No|If you specify the username parameter, you must also specify the password parameter.|
|document-id.key-delimiter|The delimiter that is used between document IDs.|No|Default value: `_`.|
|failure-handler|The fault handling policy that is used when an Elasticsearch request fails.|No|Valid values:-   fail: the default value. The job fails if the request fails.
-   ignore: The failure is ignored and the request is deleted.
-   retry\_rejected: The request that fails due to full queue capacity is retried.
-   custom class name: Fault handling is performed by using the ActionRequestFailureHandler subclass. |
|sink.flush-on-checkpoint|Specifies whether the flush operation is triggered during checkpointing.|No|Default value: true. If this feature is disabled, the connector does not wait to confirm that all pending requests are complete when Elasticsearch creates a checkpoint. Therefore, the connector does not provide the at-least-once guarantee for requests.|
|sink.bulk-flush.backoff.strategy|The retry policy of the flush operation. This parameter is required if the flush operation fails due to a temporary request error.|No|-   DISABLED: the default value. The flush operation is not retried. The flush operation fails when the first request error occurs.
-   CONSTANT: The waiting time for each flush operation is the same.
-   EXPONENTIAL: The waiting time for each flush operation increases exponentially. |
|sink.bulk-flush.backoff.max-retries|The maximum number of retries.|No|Default value: 8.|
|sink.bulk-flush.backoff.delay|The delay between retries.|No|-   CONSTANT: the delay between retries.
-   EXPONENTIAL: the initial baseline delay. |
|sink.bulk-flush.max-actions|The maximum number of flush operations that can be performed for each batch of requests.|No|Default value: 1000. If this parameter is set to 0, this feature is disabled.|
|sink.bulk-flush.max-size|The maximum memory size of the buffer in which requests are saved.|No|Default value: 2. Unit: MB. If this parameter is set to 0, this feature is disabled.|
|sink.bulk-flush.interval|The interval between flush operations.|No|Default value: 1. Unit: seconds. If this parameter is set to 0, this feature is disabled.|
|connection.path-prefix|The prefix that must be added to each REST communication.|No|This parameter is empty by default.|

## Document ID

The Elasticsearch sink can determine whether to work in upsert mode or append mode based on whether a primary key is specified. If a primary key is specified, the Elasticsearch sink works in upsert mode. In this mode, messages including UPDATE and DELETE messages are consumed. If no primary key is specified, the Elasticsearch sink works in append mode. In this mode, only INSERT messages are consumed.

In the Elasticsearch sink, primary keys are used to calculate document IDs of Elasticsearch. A document ID is a string that contains a maximum of 512 bytes without spaces. The Elasticsearch sink concatenates all primary key fields in the order defined in the DDL statement by using the key delimiter specified by document-id.key-delimiter and .generates a document ID for each row. Some data types such as BYTES, ROW, ARRAY, and MAP cannot be represented as strings. Therefore, fields of these data types cannot be used as primary key fields. If no primary key is specified, Elasticsearch automatically generates random document IDs.

## Dynamic index

The Elasticsearch sink supports both static and dynamic indexes.

-   If you use a static index, the index option value must be a string, such as myusers. All records are written to the myusers index.
-   If you use a dynamic index, you can use `{field_name}` to reference the field values in the records to dynamically generate the destination index. You can also use \{field\_name\|date\_format\_string\} to convert field values of the TIMESTAMP, DATE, and TIME types to the format specified by date\_format\_string. date\_format\_string is compatible with DateTimeFormatter in Java. For example, if you set the dynamic index to myusers-\{log\_ts\|yyyy-MM-dd\}, the record 2020-03-27 12:25:55 in the value of the log\_ts field is written to the myusers-2020-03-27 index.

## Field type mapping

Flink parses Elasticsearch data in the JSON format. For more information, see [Data type mapping](https://ci.apache.org/projects/flink/flink-docs-master/docs/connectors/table/formats/json/).

## Sample code

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
   PRIMARY KEY (user_id) NOT ENFORCED -- The primary key is optional. If you specify a primary key, it is used as the document ID. If you do not specify a primary key, the document ID is a random value.
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

