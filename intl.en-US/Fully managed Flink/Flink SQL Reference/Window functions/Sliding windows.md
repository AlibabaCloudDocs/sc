# Sliding windows

This topic describes how to use sliding window functions in Realtime Compute for Apache Flink.

## Definition

A sliding window is also known as a hop window. Unlike tumbling windows, sliding windows can overlap with each other.

A sliding window is defined by the following parameters: slide and size. The slide parameter specifies the length of a sliding step. The size parameter specifies the size of the window.

-   If the value of slide is smaller than that of size, the windows overlap with each other and each element is assigned to multiple windows.
-   If the value of slide is equal to that of size, the windows are tumbling windows.
-   If the value of slide is larger than that of size, the windows do not overlap with each other but are separated by gaps.

In most cases, most elements are assigned to multiple windows and the windows overlap with each other. Sliding windows are used to calculate moving averages. For example, to calculate the data average in the last 5 minutes every 10 seconds, set slide to 10 seconds and set size to 5 minutes. The following figure shows sliding windows for which the slide value is 30 seconds and the size value is 1 minute.

![Sliding windows](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0948209951/p34327.png)

## Syntax

You can use the HOP function in a GROUP BY clause to define a sliding window.

```
HOP(<time-attr>, <slide-interval>,<size-interval>)
<slide-interval>: INTERVAL 'string' timeUnit
<size-interval>: INTERVAL 'string' timeUnit            
```

**Note:**

The `<time-attr>` parameter must be a valid time attribute field in a stream. This parameter specifies whether the time is the processing time or the event time. For more information about how to define time attributes, see [Overview](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.md) and visit [Time attributes](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.md).

## Window identifier functions

A window identifier function specifies the start time, end time, or time attribute of a specified window. The time attribute is used to aggregate lower-level windows.

|Function|Return value type|Description|
|--------|-----------------|-----------|
|`HOP_START (<time-attr>, <slide-interval>, <size-interval>)`|TIMESTAMP|Returns the start time of the window. The boundary for the start time is included in the time span of the window. For example, if the time span of a window is `[00:10, 00:15)`, `00:10` is returned.|
|`HOP_END (<time-attr>, <slide-interval>, <size-interval>)`|TIMESTAMP|Returns the end time of the window. The boundary for the end time is included in the time span of the window. For example, if the time span of a window is `[00:00, 00:15)`, `00:15` is returned.|
|`HOP_ROWTIME (<time-attr>, <slide-interval>, <size-interval>)`|TIMESTAMP \(rowtime-attr\)|Returns the end time of the window. The boundary for the end time is excluded from the time span of the window. For example, if the time span of a window is `[00:00, 00:15)`, `00:14:59.999` is returned. The return value is a rowtime attribute based on which time operations can be performed. This function can be used in only the windows that are defined based on the event time, such as cascading windows. For more information, see [Cascading windows](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.mdsection_cwf_1kt_jhb).|
|`HOP_PROCTIME (<time-attr>, <slide-interval>, <size-interval>)`|TIMESTAMP \(rowtime-attr\)|Returns the end time of the window. The boundary for the end time is excluded from the time span of the window. For example, if the time span of a window is `[00:00, 00:15)`, `00:14:59.999` is returned. The return value is a processing time attribute based on which time operations can be performed. This function can be used in only the windows that are defined based on the processing time, such as cascading windows. For more information, see [Cascading windows](/intl.en-US/Fully managed Flink/Flink SQL Reference/Window functions/Overview.mdsection_cwf_1kt_jhb).|

## Example

In the following example, a 1-minute window slides once every 30 seconds. You can use the windows to count the number of clicks per user over the last minute every 30 seconds.

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
    CREATE TEMPORARY TABLE user_clicks (
      username VARCHAR,
      click_url VARCHAR,
      eventtime VARCHAR,                            
      ts AS TO_TIMESTAMP(eventtime),
      WATERMARK FOR ts AS ts - INTERVAL '2' SECOND   -- Define a watermark for the rowtime.
    ) with (
      'connector'='sls',
      ...
    );
    
    CREATE TEMPORARY TABLE hop_output (
      window_start TIMESTAMP,
      window_end TIMESTAMP,
      username VARCHAR,
      clicks BIGINT
    ) with (
      'connector'='datahub'           -- Log Service allows you to export only VARCHAR-type DDL statements. Therefore, DataHub is used for storage.
      ...
    );
    
    INSERT INTO
        hop_output
    SELECT
        HOP_START (ts, INTERVAL '30' SECOND, INTERVAL '1' MINUTE),
        HOP_END (ts, INTERVAL '30' SECOND, INTERVAL '1' MINUTE),
        username,
        COUNT (click_url)
    FROM
        user_clicks
    GROUP BY
        HOP (ts, INTERVAL '30' SECOND, INTERVAL '1' MINUTE),username;             
    ```

-   Test results

    |window\_start \(TIMESTAMP\)|window\_end \(TIMESTAMP\)|username \(VARCHAR\)|clicks \(BIGINT\)|
    |---------------------------|-------------------------|--------------------|-----------------|
    |`2017-10-10 09:59:30.0`|`2017-10-10 10:00:30.0`|Jark|2|
    |`2017-10-10 10:00:00.0`|`2017-10-10 10:01:00.0`|Jark|3|
    |`2017-10-10 10:00:30.0`|`2017-10-10 10:01:30.0`|Jark|2|
    |`2017-10-10 10:01:00.0`|`2017-10-10 10:02:00.0`|Jark|2|
    |`2017-10-10 10:01:30.0`|`2017-10-10 10:02:30.0`|Jark|1|
    |`2017-10-10 10:02:00.0`|`2017-10-10 10:03:00.0`|Timo|1|
    |`2017-10-10 10:02:30.0`|`2017-10-10 10:03:30.0`|Timo|1|

    If a sliding window cannot read the time at which data enters the window, the start time of the first window is moved forward. You can use the following formula to calculate the time interval by which the start time is moved forward: Time interval = Window duration - Sliding step.

    |Window duration \(seconds\)|Sliding step \(seconds\)|Event time|Start time of the first window|End time of the first window|
    |---------------------------|------------------------|----------|------------------------------|----------------------------|
    |120|30|`2019-07-31 10:00:00.0`|`2019-07-31 09:58:30.0`|`2019-07-31 10:00:30.0`|
    |60|10|`2019-07-31 10:00:00.0`|`2019-07-31 09:59:10.0`|`2019-07-31 10:00:10.0`|


