---
keyword: [InfluxDB结果表, InfluxDB]
---

# InfluxDB结果表

本文为您介绍InfluxDB结果表的DDL定义、WITH参数、类型映射和代码示例。

## DDL定义

Flink支持使用InfluxDB作为结果表输出，示例代码如下。

```
CREATE TABLE stream_test_influxdb(
 `metric` VARCHAR,
 `timestamp` BIGINT,
 `tag_value1` VARCHAR,
 `field_fieldValue1` DOUBLE
) WITH (
  'connector' = 'influxdb',
  'url' = 'http://service.cn.influxdb.aliyuncs.com:****',
  'database' = '<yourDatabaseName>',
  'username' = '<yourDatabaseUserName>',
  'password' = '<yourDatabasePassword>',
  'batchSize' ='300',
  'retentionPolicy' = 'autogen',
  'ignoreErrorData' = 'false'
);
```

建表默认格式：

-   第0列：metric（VARCHAR），必填。
-   第1列：timestamp（BIGINT），必填，单位为毫秒。
-   第2列：tag\_value1（VARCHAR），必填，最少填写一个。
-   第3列：field\_fieldValue1（DOUBLE），必填，最少填写一个。

    写入多个field\_fieldValue时，您需要按照如下格式填写。

    ```
    field_fieldValue1 类型,
    field_fieldValue2 类型,
    ...      
    field_fieldValueN 类型
    ```

    示例如下。

    ```
    field_fieldValue1 DOUBLE,
    field_fieldValue2 INTEGER,
    ...      
    field_fieldValueN INTEGER
    ```


**说明：** 结果表中只支持metric、timestamp、tag\_\*和field\_\*，不能出现其他的字段。

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为influxdb。|
|url|InfluxDB的服务地址|是|在InfluxDB中，url为VPC网络地址，例如：https://localhost:3242或http://localhost:8086。

url支持HTTP和HTTPS。 |
|database|InfluxDB的数据库名称|是|例如db-flink。|
|username|数据库的用户名|是|需要对目标数据库有写权限。用户名详情请参见[管理用户账号和数据库](https://help.aliyun.com/document_detail/113095.html?spm=a2c4g.11186623.6.711.6709769eUmPMur)。|
|password|数据库的密码|是|密码详情请参见[管理用户账号和数据库](https://help.aliyun.com/document_detail/113095.html?spm=a2c4g.11186623.6.711.6709769eUmPMur)。|
|batchSize|批量提交的记录条数|否|默认每次批量提交300条记录。|
|retentionPolicy|保留策略|否|如果您不配置该参数时，该参数会被默认填写为每个数据库的默认保留策略autogen，保留策略详情请参见[管理用户账号和数据库](https://help.aliyun.com/document_detail/113095.html?spm=a2c4g.11186623.6.711.6709769eUmPMur)。|
|ignoreErrorData|是否忽略异常数据|否|取值如下：-   true：忽略异常数据。
-   false（默认值）：不忽略异常数据。 |

## 类型映射

|InfluxDB字段类型|Flink版字段类型|
|------------|----------|
|BOOLEAN|BOOLEAN|
|INT|INT|
|BIGINT|BIGINT|
|FLOAT|FLOAT|
|DECIMAL|DECIMAL|
|DOUBLE|DOUBLE|
|DATE|DATE|
|TIME|TIME|
|TIMESTAMP|TIMESTAMP|
|VARCHAR|VARCHAR|

## 代码示例

```
CREATE TEMPORARY TABLE datahub_source(
 `metric` VARCHAR,
 `timestamp` BIGINT,
 `filedvalue` DOUBLE,
 `tagvalue` VARCHAR
) WITH (
  'connector' = 'datagen',
  'fields.metric.length' = 3,
  'fields.tagvalue.length' = 3,
  'fields.timestamp.min' = 1587539547000,
  'fields.timestamp.max' = 1619075547000,
  'fields.filedvalue.min' = 1,
  'fields.filedvalue.max' = 100000,
  'rows-per-second' = 50
);

CREATE TEMPORARY TABLE influxdb_sink(
  `metric` VARCHAR,
  `timestamp` BIGINT,
  `field_fieldValue1` DOUBLE,
  `tag_value1` VARCHAR
) WITH (
  'connector' = 'influxdb',
  'url' = 'https://***********.influxdata.tsdb.aliyuncs.com:****',
  'database' = '<yourDatabaseName>',
  'username' = '<yourDatabaseUserName>',
  'password' = '<yourDatabasePassword>',
  'batchSize' ='100',
  'retentionPolicy' = 'autogen',
  'ignoreErrorData' = 'false'
);

INSERT INTO influxdb_sink
SELECT 
   `metric`,
   `timestamp`,
   `filedvalue`,
   `tagvalue`
FROM datahub_source;
```

