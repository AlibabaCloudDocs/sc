---
keyword: [Partitioned All Cache, super-large dimension table]
---

# Partitioned All Cache

If you use the Cache All policy to join a super-large dimension table with another table, processes may fail to load full data of the dimension table into the cache. In this case, you can use the Partitioned All Cache policy to optimize the loading performance.

## Background information

When you join a dimension table with another table, you can set the cache parameter to ALL to use the Cache All policy. This policy requires that all processes load full data of the dimension table to the cache. The memory size configured for the join node must be at least twice that of the dimension table.

If the dimension table is large, the join node consumes a large amount of memory. This also increases the garbage collection overhead. If the size of a dimension table exceeds 1 TB, only partial data of the table can be loaded to the memory. Optimization by using Partitioned All Cache is introduced to resolve this issue. If Partitioned All Cache is enabled, input data is shuffled based on join keys, and each process needs to load only the required data of the dimension table to the cache.

You can set partitionedJoin to true to enable Partitioned All Cache. This reduces memory consumption. However, input data shuffling based on join keys causes additional network and CPU overheads. We recommend that you do not enable Partitioned All Cache in the following scenarios:

-   Input data is severely skewed on the join keys. If you use Partitioned All Cache in this scenario, data skew slows down the running of some nodes.
-   The size of the dimension table is small. For example, the size of the dimension table is less than 2 GB. If you enable Partitioned All Cache in this scenario, the memory consumption is slightly reduced, whereas high network and CPU overheads are generated.

## Optimization method

Add partitionedJoin = 'true' to the WITH clause of the DDL statement of the dimension table.

## Sample code

```
 CREATE TABLE white_list (
  id varchar,
  name varchar,
  age int,
  PRIMARY KEY (id), 
  PERIOD FOR SYSTEM_TIME -- The identifier of a dimension table. 
) with (
  type = 'odps',
  endPoint = 'your_end_point_name',
  project = 'your_project_name',
  tableName = 'your_table_name',
  accessId = 'your_access_id',
  accessKey = 'your_access_key', 
  `partition` = 'ds=20180905', 
  cache = 'ALL',
  partitionedJoin = 'true' -- Enable Partitioned All Cache. 
);
```

