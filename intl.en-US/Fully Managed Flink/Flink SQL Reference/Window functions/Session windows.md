# Session windows

This topic describes how to use session window functions in Realtime Compute for Apache Flink.

## Definition

A session window groups elements by session activity. Unlike tumbling and sliding windows, session windows do not overlap and are not fixed in size. If a session window does not receive elements within a specified period of time, the session is disconnected and the window is closed.

A session window is configured by using a gap, which defines the length of the inactive period. For example, a data stream that represents mouse click activities may include highly clustered mouse click events, separated by inactive periods. Data that arrives after a specified gap is assigned to a new window.

The following figure shows a session window. Different keys form different windows due to differences in data distribution.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9848209951/p34336.png)

## Syntax

The SESSION function is used to define a session window in a GROUP BY clause.

```
SESSION(<time-attr>, <gap-interval>)
<gap-interval>: INTERVAL 'string' timeUnit
```

**Note:** The `<time-attr>` parameter must be a valid time attribute in a data stream. This parameter specifies whether the time is the processing time or the event time. For more information about how to define time attributes, see [Overview](/intl.en-US/Fully Managed Flink/Flink SQL Reference/Window functions/Overview.md) and [Time Attributes](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/streaming/time_attributes.html).

## Window identifier functions

A window identifier function specifies the start time, end time, or time attribute of a specified window. The time attribute is used to aggregate lower-level windows.

|Function|Data type of return value|Description|
|--------|-------------------------|-----------|
|`SESSION_START (<time-attr>, <gap-interval>)`|Timestamp|Returns the start time of the window. The boundary for the start time is included in the time span of the window. For example, if the time span of a window is `[00:10, 00:15)`, `00:10` is returned. This is the first time recorded in the session window.|
|`SESSION_END (<time-attr>, <gap-interval>)`|Timestamp|Returns the end time of the window. The boundary for the end time is included in the time span of the window. For example, if the time span of a window is `[00:00, 00:15)`, `00:15` is returned. This is the last time recorded in the session window plus `<gap-interval>`.|
|`SESSION_ROWTIME (<time-attr>, <gap-interval>)`|TIMESTAMP \(rowtime-attr\)|Returns the end time of the window. The boundary for the end time is excluded from the time span of the window. For example, if the time span of a window is `[00:00, 00:15)`, `00:14:59.999` is returned. The return value is a rowtime attribute based on which time operations can be performed. This function can be used only in the windows that are defined based on the event time, such as cascading windows. For more information, see [Cascading windows](/intl.en-US/Fully Managed Flink/Flink SQL Reference/Window functions/Overview.mdsection_cwf_1kt_jhb). The function applies only to windows based on the event time.|
|`SESSION_PROCTIME (<time-attr>, <gap-interval>)`|TIMESTAMP \(rowtime-attr\)|Returns the end time of the window. The boundary for the end time is excluded from the time span of the window. For example, if the time span of a window is `[00:00, 00:15)`, `00:14:59.999` is returned. The return value is a processing time attribute based on which time operations can be performed. This function can be used only in the windows that are defined based on the processing time, such as cascading windows. For more information, see [Cascading windows](/intl.en-US/Fully Managed Flink/Flink SQL Reference/Window functions/Overview.mdsection_cwf_1kt_jhb). The function applies only to windows based on the processing time.|

## Example

The following example describes how to compute the number of clicks per user during each active session. The session timeout interval is 30 seconds.

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
    
    CREATE TEMPORARY TABLE session_output(
      window_start TIMESTAMP,
      window_end TIMESTAMP,
      username VARCHAR,
      clicks BIGINT
    ) with (
      'connector'='datahub'           -- Log Service allows you to export only VARCHAR-type DDL statements. Therefore, DataHub is used for storage. 
      ...
    );
    
    INSERT INTO session_output
    SELECT
    SESSION_START(ts, INTERVAL '30' SECOND),
    SESSION_END(ts, INTERVAL '30' SECOND),
    username,
    COUNT(click_url)
    FROM user_clicks
    GROUP BY SESSION(ts, INTERVAL '30' SECOND), username;
    ```

-   Test results

    |window\_start \(TIMESTAMP\)|window\_end \(TIMESTAMP\)|username \(VARCHAR\)|clicks \(BIGINT\)|
    |---------------------------|-------------------------|--------------------|-----------------|
    |`2017-10-10 10:00:00.0`|`2017-10-10 10:00:40.0`|Jark|2|
    |`2017-10-10 10:00:49.0`|`2017-10-10 10:01:35.0`|Jark|2|
    |`2017-10-10 10:01:58.0`|`2017-10-10 10:02:28.0`|Jark|1|
    |`2017-10-10 10:02:10.0`|`2017-10-10 10:02:40.0`|Timo|1|


