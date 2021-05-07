# Create a TSDB result table

This topic describes how to create a Time Series Database \(TSDB\) result table in Realtime Compute for Apache Flink. This topic also describes the parameters in the WITH clause that is used when you create a TSDB result table.

**Note:**

-   This topic applies only to Blink 2.0 and later.
-   To reference a TSDB result table in Realtime Compute for Apache Flink, you must configure a whitelist that controls access to storage resources. For more information, see [Configure a whitelist for accessing storage resources](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md).

## Introduction to TSDB

Alibaba Cloud TSDB is a database service that supports efficient reads and writes, compressed storage, and real-time computing for time series data. TSDB is widely used in Internet of Things \(IoT\) and Internet fields to implement real-time monitoring, forecasting, and alerting on devices and services.

## DDL syntax

In Realtime Compute for Apache Flink, you can use TSDB to store output data. The following code shows an example:

```
CREATE TABLE stream_test_hitsdb (
    metric      VARCHAR,   
    `timestamp` INTEGER,
    `value`     DOUBLE,
    tagk1       VARCHAR,     
    tagk2       VARCHAR,
    tagk3       VARCHAR
) WITH (
    type='hitsdb',
    host='<yourHostName>',
    virtualDomainSwitch = 'false',
    httpConnectionPool = '20',
    batchPutSize = '1000'
);
```

Default format for tables:

-   Column 0: metric \(VARCHAR\).
-   Column 1: timestamp \(INTEGER\). Unit: seconds.
-   Column 2: value \(DOUBLE\).
-   Columns 3 to N: tag keys. The field names in the time series database are used as the tag keys.

**Note:**

-   You can specify multiple tag columns.
-   You must declare the following fields: metric, timestamp, and value. The names, sequence, and data types of the fields must be the same as those in TSDB.
-   For more information about parameter settings, see [Write data](https://www.alibabacloud.com/help/doc-detail/59939.htm).

## Parameters in the WITH clause

|Parameter|Description|Remarks|
|---------|-----------|-------|
|type|The type of the result table.|Set the value to `hitsdb`.|
|host|The IP address or the domain name that is mapped to the virtual IP address \(VIP\) of the TSDB instance.|Enter the hostname of the TSDB instance. For more information, see [Connect to the instance](https://www.alibabacloud.com/help/doc-detail/56240.htm).|
|port|The port number that is used to access the TSDB instance.|Default value: 8242.|
|virtualDomainSwitch|Specifies whether to use VIPServer.|Default value: false. If you need to use VIPServer, set this parameter to true.|
|httpConnectionPool|The maximum number of connections in an HTTP connection pool.|Default value: 10.|
|httpCompress|Specifies whether to use GZIP compression.|Default value: false. This value indicates that GZIP compression is not used.|
|httpConnectTimeout|The timeout period for an HTTP connection.|Default value: 0.|
|ioThreadCount|The number of I/O threads.|Default value: 1.|
|batchPutBufferSize|The buffer size.|Default value: 10000.|
|batchPutRetryCount|The maximum number of retries for writing data to the result table.|Default value: 3.|
|batchPutSize|The maximum number of data records that can be submitted at a time.|Default value: 500.|
|batchPutTimeLimit|The maximum duration for which a data record can be stored in the buffer.|Default value: 200. Unit: milliseconds.|
|batchPutConsumerThreadCount|The number of serialized threads.|Default value: 1.|

## Models for writing data from Realtime Compute for Apache Flink to TSDB

In Blink 3.2 and later, Realtime Compute for Apache Flink can write data to TSDB by using one of the following models:

-   Write single-value data points where no tags are included. To use this model, use the specified schema that consists of three fields. The names of these fields cannot be changed. You must use the following schema format for this model:

    ```
    metric,timestamp,value
    ```

-   Write single-value data points where tags are included. To use this model, use the specified schema. In the schema, the names of the following fields cannot be changed: metric, timestamp, and value. You can specify the tag names based on your business requirements. You must use the following schema format for this model:

    ```
    metric,timestamp,value,tagKey1,....,tagKeyN
    ```

-   Write single-value data points where the number of tags is unknown. To use this model, use the specified schema that consists of four fields. The names of the four fields cannot be changed. You must use the following schema format for this model:

    ```
    metric,timestamp,value,tags
    ```

    In the schema, specify the value of the tags parameter as a JSON string. The JSON string allows you to specify an unknown number of tags. If you do not use the JSON string, you must specify a fixed number of tags for the Blink table schema. In the schema, specify the JSON string in the following format:

    ```
    {"tagKey1":"tagValue1","tagKey2":"tagValue2",……,"tagKeyN":"tagValueN"}
    ```

-   Write multi-value data points where tags are not included. You must use the following schema format for this model:

    ```
    metric,timestamp,field_name1,field_name2,……,field_nameN
    ```

    The names of the metric and timestamp fields cannot be changed. For multi-value fields, the field\_ prefix is used for each field to distinguish fields from tags and to support single-value data writes. For example, when the `field_name1` field is written to TSDB, the prefix `field_` is automatically removed. In the preceding schema, name1 and name2 are the names of the multi-value fields. For the preceding schema, the following format is used to write data to TSDB:

    ```
    metric,timestamp,name1,name2,……,nameN
    ```

-   Write multi-value data points where tags are included. To use this model, use the specified schema. In the schema, the names of the following fields cannot be changed: metric and timestamp. You can specify the tag names based on your business requirements. You must use the following schema format for this model:

    ```
    metric,timestamp,tagKey1,....,tagKeyN,field_name1,field_name2,……,field_nameN
    ```

-   Write single-value data points where the number of tags is unknown. You must use the following schema format for this model:

    ```
    metric,timestamp,tags,field_name1,field_name2,……,field_nameN
    ```

    The content of tags is a JSON string shown in the following example. Therefore, the limit that the Blink table schema requires a fixed number of tags does not apply.

    ```
    {"tagKey1":"tagValue1","tagKey2":"tagValue2",……,"tagKeyN":"tagValueN"}
    ```


## FAQ

Q: Why does the following error occur during a failover "The values of the LONG data type cannot be converted into the values of the INT data type"?

A: Blink versions earlier than 2.2.5 support only the INT data type. Blink 2.2.5 and later versions support the BIGINT data type.

