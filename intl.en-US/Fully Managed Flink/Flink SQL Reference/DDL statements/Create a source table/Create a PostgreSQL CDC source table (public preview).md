---
keyword: [CDC for PostgreSQL, Change Data Capture, CDC]
---

# Create a PostgreSQL CDC source table \(public preview\)

This topic describes how to create a PostgreSQL Change Data Capture \(CDC\) source table. It also describes the data definition language \(DDL\) statements, parameters in the WITH clause, field type mappings, and sample code used when you create a PostgreSQL CDC source table.

**Note:** PostgreSQL CDC source tables are in public preview. If you have high requirements for job stability, we recommend that you do not use PostgreSQL CDC source tables.

## Introduction to PostgreSQL CDC source tables

A PostgreSQL CDC source table is a streaming source table of PostgreSQL. This table is used to read full snapshot data and change data in sequence from a PostgreSQL database. The exactly-once processing semantics is used to ensure data accuracy even if a failure occurs.

**Note:** If you use an open source PostgreSQL database, the PostgreSQL CDC connector can read data from databases of PostgreSQL V9.6 or later.

## DDL syntax

```
CREATE TABLE postgrescdc_source (
  shipment_id INT,
  order_id INT,
  origin STRING,
  destination STRING,
  is_arrived BOOLEAN
) WITH (
  'connector' = 'postgres-cdc',
  'hostname' = '<yourHostname>',
  'port' = '5432',
  'username' = '<yourUserName>',
  'password' = '<yourPassWord>',
  'database-name' = '<yourDatabaseName>',
  'schema-name' = '<yourSchemaName>',
  'table-name' = '<yourTableName>'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Data type|Remarks|
|---------|-----------|--------|---------|-------|
|connector|The type of the source table.|Yes|STRING|Set the value to `postgres-cdc`.|
|hostname|The IP address or hostname of the PostgreSQL database.|Yes|STRING|None.|
|username|The username that is used to access the PostgreSQL database service.|Yes|STRING|None.|
|password|The password that is used to access the PostgreSQL database service.|Yes|STRING|None.|
|database-name|The name of the PostgreSQL database.|Yes|STRING|The database name can be a regular expression so that data of multiple databases can be read.|
|schema-name|The schema name of the PostgreSQL database.|Yes|STRING|The schema name can be a regular expression so that data of multiple schemas can be read.|
|table-name|The name of the PostgreSQL table.|Yes|STRING|The table name can be a regular expression so that data of multiple tables can be read.|
|port|The port that is used to access the PostgreSQL database service.|No|INTEGER|Default value: 5432.|
|decoding.plugin.name|The name of the PostgreSQL logical decoding plug-in.|No|STRING|The plug-in name is determined based on the plug-in that is installed in the PostgreSQL database service. Valid values: -   decoderbufs \(default value\)
-   wal2json
-   wal2json\_rds
-   wal2json\_streaming
-   wal2json\_rds\_streaming
-   pgoutput

**Note:** If you use ApsaraDB RDS for PostgreSQL, you must enable the logical decoding feature that is supported by the wal2json plug-in. For more information, see [Use the wal2json plug-in](/intl.en-US/RDS PostgreSQL Database/Proprietary AliPG/Use the wal2json plug-in.md). |
|debezium.\*|The Debezium property parameters.|No|STRING|This parameter is used to impose fined-grained control over the behavior of Debezium clients. For example, you can set `debezium.snapshot.mode` to never. For more information, see [Configure properties](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-connector-properties). **Note:** We recommend that you set the `debezium.slot.name` parameter for each table to prevent the following error: `PSQLException: ERROR: replication slot "debezium" is active for PID 974`. |

## Field type mapping

The following table lists the data type mappings between PostgreSQL CDC and Flink fields.

|Data type of PostgreSQL CDC|Data type of Flink|
|---------------------------|------------------|
|SMALLINT|SMALLINT|
|INT2|
|SMALLSERIAL|
|SERIAL2|
|INTEGER|INT|
|SERIAL|
|BIGINT|BIGINT|
|BIGSERIAL|
|BIGINT|BIGINT|
|REAL|FLOAT|
|FLOAT4|
|FLOAT8|DOUBLE|
|DOUBLE PRECISION|
|NUMERIC\(p, s\)|DECIMAL\(p, s\)|
|DECIMAL\(p, s\)|
|BOOLEAN|BOOLEAN|
|DATE|DATE|
|TIME \[\(p\)\] \[WITHOUT TIMEZONE\]|TIME \[\(p\)\] \[WITHOUT TIMEZONE\]|
|TIMESTAMP \[\(p\)\] \[WITHOUT TIMEZONE\]|TIMESTAMP \[\(p\)\] \[WITHOUT TIMEZONE\]|
|CHAR\(n\)|STRING|
|CHARACTER\(n\)|
|VARCHAR\(n\)|
|CHARACTER VARYING\(n\)|
|TEXT|
|BYTEA|BYTES|

