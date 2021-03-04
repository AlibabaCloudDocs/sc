# Tumbling windows

This topic describes how to use tumbling window functions in Realtime Compute for Apache Flink.

## Definition

A tumbling window assigns segments of a data stream to a window that has a specified size. Generally, tumbling windows are fixed in size and do not overlap with each other. For example, if a 5-minute tumbling window is defined, an infinite data stream is divided based on the time period into windows such as `[0:00, 0:05)`, `[0:05, 0:10)`, and `[0:10, 0:15)`. The following figure shows a 30-second tumbling window.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0948209951/p34298.png)

## Syntax

You can use the TUMBLE function in a GROUP BY clause to define a tumbling window.

```
TUMBLE(<time-attr>, <size-interval>)
<size-interval>: INTERVAL 'string' timeUnit
```

**Note:** The `<time-attr>` parameter must be a valid time attribute field in a time stream. This parameter specifies whether the time is the processing time or the event time. For more information about how to define time attributes, see [Overview](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.md) and visit [Time Attributes](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/streaming/time_attributes.html).

## Window identifier functions

A window identifier function specifies the start time, end time, or time attribute of a specified window. The time attribute is used to aggregate lower-level windows.

|Function|Return value type|Description|
|--------|-----------------|-----------|
|`TUMBLE_START(time-attr, size-interval)`|TIMESTAMP|Returns the start time of the window. The boundary for the start time is included in the time span of the window. For example, if the time span of a window is `[00:10, 00:15)`, `00:10` is returned.|
|`TUMBLE_END(time-attr, size-interval)`|TIMESTAMP|Returns the end time of the window. The boundary for the end time is included in the time span of the window. For example, if the time span of a window is `[00:00, 00:15]`, `00:15` is returned.|
|`TUMBLE_ROWTIME(time-attr, size-interval)`|TIMESTAMP\(rowtime-attr\)|Returns the end time of the window. The boundary for the end time is excluded from the time span of the window. For example, if the time span of a window is `[00:00, 00:15]`, `00:14:59.999` is returned. The return value is a rowtime attribute based on which time operations can be performed. This function can be used in only the windows that are defined based on the event time, such as cascading windows. For more information, see [Cascading windows](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.mdsection_cwf_1kt_jhb).|
|`TUMBLE_PROCTIME(time-attr, size-interval)`|TIMESTAMP\(rowtime-attr\)|Returns the end time of the window. The boundary for the end time is excluded from the time span of the window. For example, if the time span of a window is `[00:00, 00:15]`, `00:14:59.999` is returned. The return value is a processing time attribute based on which time operations can be performed. For example, this function can be used in only the windows that are defined based on the processing time, such as cascading windows. For more information, see [Cascading windows](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.mdsection_cwf_1kt_jhb).|

## Example 1: Count the number of clicks per user per minute for a specific website based on the event time

-   Test data

    |username \(VARCHAR\)|click\_url \(VARCHAR\)|ts \(TIMESTAMP\)|
    |--------------------|----------------------|----------------|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:00:00.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:00:10.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:00:49.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:01:05.0`|
    |Jark|`http://taobao.com/xxx`|`2017-10-10 10:01:58.0`|
    |Timo|`http://taobao.com/xxx`|`2017-10-10 10:02:10.0`|

-   Test statements

    ```
    CREATE TEMPORARY TABLE user_clicks(
      username varchar,
      click_url varchar,
      eventtime varchar,                            
      ts AS TO_TIMESTAMP(eventtime),
      WATERMARK FOR ts AS ts - INTERVAL '2' SECOND   -- Define a watermark for the rowtime.
    ) with (
      'connector'='sls',
      ...
    );
    
    CREATE TEMPORARY TABLE tumble_output(
      window_start TIMESTAMP,
      window_end TIMESTAMP,
      username VARCHAR,
      clicks BIGINT
    ) with (
      'connector'='datahub'          -- Log Service allows you to export only VARCHAR-type DDL statements. Therefore, DataHub is used for storage.
      ...
    );
    
    INSERT INTO tumble_output
    SELECT
    TUMBLE_START(ts, INTERVAL '1' MINUTE) as window_start,
    TUMBLE_END(ts, INTERVAL '1' MINUTE) as window_end,
    username,
    COUNT(click_url)
    FROM user_clicks
    GROUP BY TUMBLE(ts, INTERVAL '1' MINUTE),username;
    ```

-   Test results

    |window\_start \(TIMESTAMP\)|window\_end \(TIMESTAMP\)|username \(VARCHAR\)|clicks \(BIGINT\)|
    |---------------------------|-------------------------|--------------------|-----------------|
    |`2017-10-10 10:00:00.0`|`2017-10-10 10:01:00.0`|Jark|3|
    |`2017-10-10 10:01:00.0`|`2017-10-10 10:02:00.0`|Jark|2|
    |`2017-10-10 10:02:00.0`|`2017-10-10 10:03:00.0`|Timo|1|


## Example 2: Count the number of clicks per user per minute for a specific website based on the processing time

-   Test data

    |username \(VARCHAR\)|click\_url \(VARCHAR\)|
    |--------------------|----------------------|
    |Jark|`http://taobao.com/xxx`|
    |Jark|`http://taobao.com/xxx`|
    |Jark|`http://taobao.com/xxx`|
    |Jark|`http://taobao.com/xxx`|
    |Jark|`http://taobao.com/xxx`|
    |Timo|`http://taobao.com/xxx`|

-   Test statements

    ```
    CREATE TEMPORARY TABLE window_test (
      username   VARCHAR,
      click_url  VARCHAR,
      ts as PROCTIME()
    ) WITH (
      'connector'='sls',
      ...
    );
    
    CREATE TEMPORARY TABLE tumble_output(
      window_start TIMESTAMP,
      window_end TIMESTAMP,
      username VARCHAR,
      clicks BIGINT
    ) with (
      'connector'='datahub'              -- Log Service allows you to export only VARCHAR-type DDL statements. Therefore, DataHub is used for storage.
      ...
    );
    
    INSERT INTO tumble_output
    SELECT
    TUMBLE_START(ts, INTERVAL '1' MINUTE),
    TUMBLE_END(ts, INTERVAL '1' MINUTE),
    username,
    COUNT(click_url)
    FROM window_test
    GROUP BY TUMBLE(ts, INTERVAL '1' MINUTE), username;
    ```

-   Test results

    |window\_start \(TIMESTAMP\)|window\_end \(TIMESTAMP\)|username \(VARCHAR\)|clicks \(BIGINT\)|
    |---------------------------|-------------------------|--------------------|-----------------|
    |`2019-04-11 14:43:00.000`|`2019-04-11 14:44:00.000`|Jark|5|
    |`2019-04-11 14:43:00.000`|`2019-04-11 14:44:00.000`|Timo|1|


