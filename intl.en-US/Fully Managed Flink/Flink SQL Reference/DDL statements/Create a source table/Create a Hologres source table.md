---
keyword: [, ]
---

# Create a Hologres source table

取消交付

**Note:**

-   [Manage a foreign table](/intl.en-US/Common Development Tools/HoloWeb/Connection Management/MaxCompute Acceleration/Manage a foreign table.md)
-   
[What is Hologres?](/intl.en-US/Product Introduction/What is Hologres?.md)

```
create table hologres_source(
  name varchar,
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
   
);
```

**Note:**

-   -   [\#section\_lih\_mxx\_vik](#section_lih_mxx_vik)

| | | | |
|--|--|--|--|
|connector| | ||
|dbname| | | |
|tablename|**Note:** 

| | |
|username| | | |
|password| | | |
|endpoint| | |[Endpoints for connecting to Hologres](/intl.en-US/Manage Instances/Endpoints for connecting to Hologres.md)|
|field\_delimiter|**Note:** 

| ||
|binlog| | |-   -   
**Note:** |
|binlogMaxRetryTimes| | |**Note:** |
|binlogRetryIntervalMs| | |**Note:** |
|binlogBatchReadSize| | |**Note:** |
|cdcMode| | |-   -   
**Note:** |

```
CREATE TEMPORARY TABLE hologres_source (
  name varchar, 
  age BIGINT,
  birthday BIGINT
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
   
);

CREATE TEMPORARY TABLE blackhole_sink(
  name varchar,
  age BIGINT,
  birthday BIGINT 
) with (
  'connector'='blackhole'
);

INSERT INTO blackhole_sink
SELECT 
   name, age, birthday
from hologres_source;
```

| | |
|--|--|
|INT|INT|
|INT\[\]|ARRAY|
|BIGINT|BIGINT|
|BIGINT\[\]|ARRAY|
|REAL|FLOAT|
|REAL\[\]|ARRAY|
|DOUBLE PRECISION|DOUBLE|
|DOUBLE PRECISION\[\]|ARRAY|
|BOOLEAN|BOOLEAN|
|BOOLEAN\[\]|ARRAY|
|TEXT|VARCHAR|
|TEXT\[\]|ARRAY|
|NUMERIC|DECIMAL|
|DATE|DATE|
| |TIMESTAMP|

-   -   -   -   -   -   

```
begin;
create table test_message_src(
 id int primary key, 
 title text not null, 
 body text
);
call set_table_property('test_message_src', 'orientation', 'row');
call set_table_property('test_message_src', 'clustering_key', 'id');
call set_table_property('test_message_src', 'binlog.level', 'replica'); 
call set_table_property('test_message_src', 'binlog.ttl', '86400'); 
commit;
```

-   -   -   ```
select tg.property_value from hologres.hg_table_properties tb join hologres.hg_table_group_properties tg on tb.property_value = tg.tablegroup_name where tb.property_key = 'table_group' and tg.property_key = 'shard_count' and table_name = '<tablename>';
```

-   -   ```
create table test_message_src_binlog_table(
  hg_binlog_lsn BIGINT,
  hg_binlog_event_type BIGINT,
  hg_binlog_timestamp_us BIGINT,
  id INTEGER,
  title VARCHAR,
  body VARCHAR
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'binlog' = 'true',
  'binlogMaxRetryTimes' = '10',
  'binlogRetryIntervalMs' = '500',
  'binlogBatchReadSize' = '100'
);
```

-   ```
create table test_message_src_binlog_table(
  id INTEGER,
  title VARCHAR,
  body VARCHAR
) with (
  'connector'='hologres',
  'dbname'='<yourDbname>',
  'tablename'='<yourTablename>',
  'username'='<yourAccessID>',
  'password'='<yourAccessSecret>',
  'endpoint'='<yourEndpoint>',
  'binlog' = 'true',
  'cdcMode' = 'true',
  'binlogMaxRetryTimes' = '10',
  'binlogRetryIntervalMs' = '500',
  'binlogBatchReadSize' = '100'
);
```

-   | | | |
|--|--|--|
|hg\_binlog\_lsn|BIGINT| |
|hg\_binlog\_event\_type|BIGINT|-   -   -   -   |
|hg\_binlog\_timestamp\_us|BIGINT| |
|user\_table\_column\_1| | |
|...|...| |
|user\_table\_column\_n| | |


