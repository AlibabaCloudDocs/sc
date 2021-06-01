---
keyword: [结果表, MaxCompute]
---

# 创建MaxCompute结果表

本文为您介绍如何创建MaxCompute结果表，以及创建过程中及到的WITH参数、类型映射和常见问题。

**说明：**

-   仅Blink 1.5.1及以上版本支持MaxCompute结果表。
-   MaxCompute中的Clustered Table表不支持作为MaxCompute结果表。

## 实现原理

MaxCompute Sink可以分为以下两个阶段：

1.  写入数据。调用MaxCompute SDK中的接口将数据写入缓冲区，在缓冲区大小超过64 MB或者每隔指定的时间间隔时，上传数据到MaxCompute的临时文件中。
2.  提交会话。在任务进行Checkpoint时， MaxCompute Sink会调用Tunnel的Commit方法，提交会话，移动临时文件到MaxCompute表的数据目录，并修改元数据。

    **说明：** Commit方法不能提供原子性。因此，MaxCompute Sink提供的是At least Once方式，而不是Exactly Once方式。


## 语法示例

实时计算Flink版支持使用MaxCompute作为结果输出，示例代码如下。

**说明：** DDL中定义的字段需要与MaxCompute物理表中的字段名称、顺序以及类型保持一致，否则可能导致MaxCompute物理表中查询的数据为`/n`。

```
create table odps_output(
  id INT,
  user_name VARCHAR,
  content VARCHAR
) with (
  type = 'odps',
  endPoint = '<YourEndPoint>',
  project = '<YourProjectName>',
  tableName = '<YourtableName>',
  accessId = '<yourAccessKeyId>',
  accessKey = '<yourAccessKeySecret>',
  `partition` = 'ds=2018****'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|type|结果表类型。|是|固定值为`odps`。|
|endPoint|MaxCompute服务地址。|是|参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。|
|tunnelEndpoint|MaxCompute Tunnel服务的连接地址。|是|参见[开通MaxCompute服务的Region和服务连接对照表](/cn.zh-CN/准备工作/配置Endpoint.md)。**说明：** VPC环境下必填。 |
|project|MaxCompute项目名称。|是|无。|
|tableName|表名。|是|无。|
|accessId|AccessKey ID。|是|无。|
|accessKey|AccessKey Secret。|是|无。|
|partition|分区名。|否|如果存在分区表则必填： -   固定分区

例如``partition` = 'ds=20180905'`表示将数据写入分区`ds= 20180905`。

-   动态分区（Blink 3.2.1及以上版本支持）

如果不明文显示分区的值，则会根据写入数据中的分区列具体的值，写入到不同的分区中。例如``partition`='ds'`表示根据`ds`字段的值写入分区。

如果要创建多级动态分区，With参数中Partition的字段顺序和结果表的DDL中的分区字段顺序，必须与物理表一致，各个分区字段之间使用逗号（,）分割。

**说明：**

    -   动态分区列需要显式写在建表语句中。
    -   对于动态分区字段为空的情况，如果数据源中`ds=null`或者`ds=''`，则输出结果为：
        -   3.2.1及以前的版本会抛出空指针异常（NPE）。
        -   3.2.2及以后的版本会创建ds=NULL的分区。 |
|flushIntervalMs|Odps tunnel writer缓冲区Flush间隔，单位毫秒。 MaxCompute Sink写入记录时，先放入数据到MaxCompute的缓冲区中，等缓冲区溢出或者每隔一段时间（flushIntervalMs）时，再把缓冲区里的数据写到目标 MaxCompute表。

|否|默认值是30000毫秒，即30秒。**说明：** Blink 3.6.0及以上版本支持该参数。 |
|partitionBy|写入Sink节点前，系统会根据该值做Hash Shuffle，数据就会流向对应的Sink节点。|否|系统按照多个列进行Hash Shuffle，各个列名之间使用逗号（,）分割。默认为空。**说明：** Blink 3.6.0及以上版本支持该参数。 |
|isOverwrite|写入Sink节点前，是否将结果表清空。|否|-   Blink 3.2以下版本默认参数值为true。
-   Blink 3.2及以上版本默认参数值为false。在声明MaxCompute的流式作业结果表时，isOverwrite参数必须为false，否则在编译时会抛出异常。

**说明：** Blink 3.2及以上版本支持isOverwrite参数值变更为true。Blink 3.2以下版本如需变更isOverwrite参数值，请升级版本。 |
|dynamicPartitionLimit|分区数目最大值。|否|默认值是100，内存中会把出现过的分区和Tunnel/writer的映射关系维护到一个Map里，如果这个Map的大小超过了dynamicPartitionLimit设定值，则会出现`Too many dynamic partitions: 100, which exceeds the size limit: 100`报错。|

## 类型映射

|MaxCompute字段类型|实时计算Flink版字段类型|
|--------------|--------------|
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

包含MaxCompute结果表的实时计算Flink版作业代码示例如下：

-   写入固定分区

    ```
    CREATE TABLE source (
       id INT,
       len INT,
       content VARCHAR
    ) with (
       type = 'random'
    );
    
    create table odps_sink (
       id INT,
       len INT,
       content VARCHAR
    ) with (
       type = 'odps',
       endPoint = '<yourEndpoint>', 
       project = '<yourProjectName>',
       tableName = '<yourTableName>',
       accessId = '<yourAccessId>',
       accessKey = '<yourAccessPassword>',
       `partition` = 'ds=20180418'
    );
    
    INSERT INTO odps_sink 
    SELECT 
       id, len, content 
    FROM source;
    ```

-   写入动态分区

    ```
    CREATE TABLE source (
      id INT,
      len INT,
      content VARCHAR,
      c TIMESTAMP 
    ) with (
      type = 'random'
    );
    
    create table odps_sink (
      id INT,
      len INT,
      content VARCHAR,
      ds VARCHAR --动态分区列需要显式写在建表语句中。
    ) with (
      type = 'odps',
      endPoint = '<yourEndpoint>', 
      project = '<yourProjectName>',
      tableName = '<yourTableName>',
      accessId = '<yourAccessId>',
      accessKey = '<yourAccessPassword>',
      `partition`='ds' --不写分区的值，表示根据ds字段的值写入不同分区。
    );
    
    INSERT INTO odps_sink 
    SELECT 
       id, 
       len, 
       content,
       DATE_FORMAT(c, 'yyMMdd') as ds
    FROM source;
    ```


## 常见问题

-   Q：公共云的endPoint和tunnelEndpoint是什么？配置错误会产生什么结果？

    A：endPoint和tunnelEndpoint参数说明参见[配置Endpoint](/cn.zh-CN/准备工作/配置Endpoint.md)。VPC环境中这两个参数如果配置错误可能会导致任务异常。

    -   endPoint配置错误：任务上线停滞在91%的进度。
    -   tunnelEndpoint配置错误：任务运行失败。
-   Q: Stream模式的MaxCompute结果表是否支持isOverwrite为true？

    A：Blink 3.2以下版本支持，Blink3.2及以上版本不支持。

    isOverwrite为true，即写入结果表之前会把结果表或者结果数据清空。作业每次启动后和暂停恢复后、写入之前会把原来结果表或者结果分区里的内容删除。

    -   Blink 3.2以下版本isOverwrite默认值是true，且不支持修改。流式作业完成暂停或恢复操作后，会造成数据丢失。
    -   Blink 3.2及以上版本isOverwrite默认值是false，且在声明MaxCompute流式作业结果表时，isOverwrite参数值需要设置为false，否则在编译时会抛出异常。Stream模式的MaxCompute结果表具备At Least Once数据保障机制，在作业运行失败后，可能会出现数据重复。

