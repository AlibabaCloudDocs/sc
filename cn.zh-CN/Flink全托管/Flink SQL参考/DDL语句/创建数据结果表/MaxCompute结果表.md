---
keyword: [结果表, MaxCompute]
---

# MaxCompute结果表

本文为您介绍MaxCompute结果表的DDL定义、WITH参数、类型映射和代码示例。

**说明：** MaxCompute Connector可以作为Stream作业和Batch作业的结果表使用。

## 什么是MaxCompute

大数据计算服务MaxCompute（原名ODPS）是一种快速、完全托管的EB级数据仓库解决方案，致力于批量结构化数据的存储和计算，提供海量数据仓库的解决方案及分析建模服务。MaxCompute详情请参见[什么是MaxCompute](/cn.zh-CN/产品简介/什么是MaxCompute.md)。

## 实现原理

MaxCompute Sink可以分为以下两个阶段：

1.  写入数据。调用MaxCompute SDK中的接口将数据写入缓冲区，在缓冲区大小超过64 MB或者每隔指定的时间间隔时，上传数据到MaxCompute的临时文件中。
2.  提交会话。在任务进行Checkpoint时， MaxCompute Sink会调用Tunnel的Commit方法，提交会话，移动临时文件到MaxCompute表的数据目录，并修改元数据。

    **说明：** Commit方法不能提供原子性。因此，MaxCompute Sink提供的是At least Once方式，而不是Exactly Once方式。


## DDL定义

Flink支持将MaxCompute作为结果输出，示例代码如下。

```
create table odps_sink(
  id INT,
  user_name VARCHAR,
  content VARCHAR
) WITH (
  'connector' = 'odps',
  'endpoint' = '<yourEndpoint>',
  'tunnelEndpoint' = '<yourTunnelEndpoint>',
  'project' = '<yourProjectName>',
  'tablename' = '<yourTableName>',
  'accessid' = '<yourAccessKeyId>',
  'accesskey' = '<yourAccessKeySecret>',
  'partition' = 'ds=2018****'
);
```

**说明：**

-   WITH参数需要全部小写。
-   DDL定义中的字段名称、顺序和类型必须与MaxCompute物理表保持一致，否则可能导致MaxCompute物理表中查询的数据为`/n`。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型。|是|固定值为`odps`。|
|endPoint|MaxCompute服务地址。|是|请参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址。|是|请参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。|
|project|MaxCompute项目名称。|是|无。|
|tableName|表名。|是|无。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|partition|分区名。|否|如果存在分区表，则必填partition。填写partition需要注意以下两点：-   固定分区

例如``partition` = 'ds=20180905'`表示将数据写入分区`ds= 20180905`。

-   动态分区

如果未明文显示分区值，则根据写入数据中分区列具体值，写入对应分区。例如``partition`='ds'`表示根据`ds`字段值写入对应分区。

如果要创建多级动态分区，Partition中多个字段顺序必须和MaxCompute物理表保持一致，各个分区字段之间使用逗号（,）分割。

**说明：**

    -   动态分区列需要显式写在建表语句中。
    -   在动态分区字段为空时，如果数据源中`ds=null`或者`ds=''`，则输出结果为ds=NULL的分区。 |
|flushIntervalMs|Odps tunnel writer缓冲区Flush间隔。 MaxCompute Sink写入记录时，先将数据存储到MaxCompute的缓冲区中，等缓冲区溢出或者每隔一段时间（flushIntervalMs），再把缓冲区里的数据写到目标 MaxCompute表。

|否|单位为毫秒，默认值为30000（30秒）。|
|dynamicPartitionLimit|分区数目最大值。|否|默认值为100，系统会把已写入的分区和TunnelBufferedWriter的映射关系维护到一个Map里，如果该Map大小超过了dynamicPartitionLimit设定值，则会出现`Too many dynamic partitions: 100, which exceeds the size limit: 100`报错。|

## 类型映射

|MaxCompute字段类型|Flink字段类型|
|--------------|---------|
|TINYINT|TINYINT|
|SMALLINT|SMALLINT|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|DATETIME|TIMESTAMP|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|
|STRING|VARCHAR|
|DECIMAL|DECIMAL|

## 代码示例

-   写入固定分区

    ```
    CREATE TEMPORARY TABLE datagen_source (
      id INT,
      len INT,
      content VARCHAR
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE odps_sink (
      id INT,
      len INT,
      content VARCHAR
    ) WITH (
      'connector' = 'odps',
      'endpoint' = '<yourEndpoint>',
      'tunnelEndpoint' = '<yourTunnelEndpoint>',
      'project' = '<yourProjectName>',
      'tablename' = '<yourTableName>',
      'accessid' = '<yourAccessKeyId>',
      'accesskey' = '<yourAccessKeySecret>',
      'partition' = 'ds=20180905'
    );
    
    INSERT INTO odps_sink 
    SELECT 
      id, len, content 
    FROM datagen_source;
    ```

-   写入动态分区

    ```
    CREATE TEMPORARY TABLE datagen_source (
      id INT,
      len INT,
      content VARCHAR,
      c TIMESTAMP 
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE odps_sink (
      id  INT,
      len INT,
      content VARCHAR,
      ds VARCHAR --动态分区列需要显式声明。
    ) WITH (
      'connector' = 'odps',
      'endpoint' = '<yourEndpoint>',
      'tunnelEndpoint' = '<yourTunnelEndpoint>',
      'project' = '<yourProjectName>',
      'tablename' = '<yourTableName>',
      'accessid' = '<yourAccessKeyId>',
      'accesskey' = '<yourAccessKeySecret>',
      'partition' ='ds' --不写分区的值，表示根据ds字段的值写入不同分区。
    );
    
    INSERT INTO odps_sink 
    SELECT 
       id, 
       len, 
       content,
       DATE_FORMAT(c, 'yyMMdd') as ds
    FROM datagen_source;
    ```


