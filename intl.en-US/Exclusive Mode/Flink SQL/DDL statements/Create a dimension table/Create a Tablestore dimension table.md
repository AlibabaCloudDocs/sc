# Create a Tablestore dimension table

This topic describes how to create a Tablestore dimension table in Realtime Compute for Apache Flink.

**Note:** This topic applies only to Blink 1.4.5 and later.

## Introduction to Tablestore

Tablestore is a distributed NoSQL database service that is built on the Apsara distributed operating system of Alibaba Cloud. Tablestore adopts sharding and load balancing technologies to scale out services and handle concurrent transactions. You can use Tablestore to store and query large amounts of structured data in real time.

## Example

In Realtime Compute for Apache Flink, you can use a Tablestore table as a dimension table. The following code shows an example:

```
CREATE TABLE ots_dim_table (
 id int,
 len int,
 content VARCHAR,
 PRIMARY KEY (id),
 PERIOD FOR SYSTEM_TIME--Define the change period of the dimension table.
) WITH (
 type='ots',
 endPoint='<yourEndpoint>',
 instanceName='<yourInstanceName>',
 tableName='<yourTableName>',
 accessId='<yourAccessId>',
 accessKey='<yourAccessKey>'
);
```

**Note:**

-   When you declare a dimension table, you must specify a primary key.
-   When you join a dimension table with another table, the ON clause must contain the equivalent \(=\) conditions for all the primary key fields.
-   The primary key of a Tablestore table is the row key of the table.

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|type|The type of the dimension table.|Set the value to `ots`.|
|endPoint|The endpoint of the Tablestore instance.|Enter the VPC endpoint if the instance is deployed in a VPC.|
|instanceName|The name of the Tablestore instance.|None.|
|tableName|The name of the Tablestore dimension table.|None.|
|accessId|The AccessKey ID that is used to access Tablestore.|None.|
|accessKey|The AccessKey secret that is used to access Tablestore.|None.|

## Parameters in the CACHE clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|cache|The cache policy.|Valid values: -   None: indicates that data is not cached. This is the default cache policy.
-   LRU: indicates that only the specified data in the dimension table is cached. The system searches the cache each time it receives a data record. If the system does not find the record in the cache, it searches for the data record in the physical dimension table.

If you use this cache policy, you must specify the cacheSize and cacheTTLMs parameters. |
|cacheSize|The maximum number of rows that can be cached.|You can set this parameter when the cache parameter is set to LRU. Default value: 10000.|
|cacheTTLMs|The cache timeout period. Unit: milliseconds.|You can set this parameter when the cache parameter is set to LRU.|

## Sample code

```
CREATE TABLE datahub_input1 (
id            BIGINT,
name        VARCHAR,
age           BIGINT
) WITH (
type='datahub'
);

CREATE TABLE phoneNumber(
name VARCHAR,
phoneNumber bigint,
primary key(name),
PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table.
)with(
type='ots'
);

CREATE TABLE result_infor(
id bigint,
phoneNumber bigint,
name VARCHAR
)with(
type='rds'
);

INSERT INTO result_infor
SELECT
t.id,
w.phoneNumber,
t.name
FROM datahub_input1 as t
JOIN phoneNumber FOR SYSTEM_TIME AS OF PROCTIME() as w --You must include this clause when you perform a JOIN operation on the dimension table.
ON t.name = w.name;     
```

For more information about the syntax for dimension tables, see [JOIN statements for dimension tables](/intl.en-US/Exclusive Mode/Flink SQL/Query statements/JOIN statements for dimension tables.md).

