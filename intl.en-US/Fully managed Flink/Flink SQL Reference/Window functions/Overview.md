# Overview

This topic describes the window functions, time attributes, and window types that Flink SQL supports.

## Window functions

Flink SQL supports aggregation over infinite windows. Windows do not need to be explicitly defined in SQL statements. Flink SQL also supports aggregation over a specific window. For example, to count the number of users who clicked a URL in the last minute, you can define a window to collect data about user clicks for the previous minute. Then, you can compute the data in the window to obtain the result.

Flink SQL supports window aggregates and over aggregates. This topic describes window aggregates. Window aggregates support the windows that are defined based on the following two time attributes: event time and processing time. For each time attribute, Flink SQL supports three window types: tumbling window, sliding window, and session window.

## Time attributes

Flink SQL supports two time attributes: event time and processing time. Realtime Compute for Apache Flink aggregates data in windows based on these time attributes.

-   The event time attribute specifies the time when each data entry is created. It is provided in the schema.
-   The processing time attribute specifies the local system time at which the system processes an event.

**Note:** For more information about the time attributes in Apache Flink, visit [Time Attributes](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/streaming/time_attributes.html).

## Cascading windows

The event time attribute of the rowtime column no longer takes effect after a window operation is complete. You can use a helper function such as `TUMBLE_ROWTIME`, `HOP_ROWTIME`, or `SESSION_ROWTIME` to obtain `max(rowtime)` of the rowtime column in a window. You can use the obtained value as the rowtime of the time window. The value is `window_end - 1` and is of the TIMESTAMP data type. The TIMESTAMP value has the rowtime attribute. For example, if the time span of a window is `[00:00, 00:15)`, `00:14:59.999` is returned.

In the following example, 1-hour tumbling windows are used to aggregate data based on the aggregation results of 1-minute tumbling windows. This helps you meet various window requirements.

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
  'connector'='datahub'        -- Log Service allows you to export only VARCHAR-type DDL statements. Therefore, DataHub is used for storage.
  ...
);

CREATE TEMPORARY VIEW one_minute_window_output AS 
SELECT 
  TUMBLE_ROWTIME(ts, INTERVAL '1' MINUTE) as rowtime,  -- Use TUMBLE_ROWTIME as the aggregation time of the level-two window.
  username, 
  COUNT(click_url) as cnt
FROM user_clicks
GROUP BY TUMBLE(ts, INTERVAL '1' MINUTE),username;

BEGIN statement set;
INSERT INTO tumble_output
SELECT
  TUMBLE_START(rowtime, INTERVAL '1' HOUR),
  TUMBLE_END(rowtime, INTERVAL '1' HOUR),
  username,
  SUM(cnt)
FROM one_minute_window_output
GROUP BY TUMBLE(rowtime, INTERVAL '1' HOUR), username;
END;
```

