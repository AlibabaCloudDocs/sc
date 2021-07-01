# UNIX\_TIMESTAMP

This topic describes how to use the UNIX\_TIMESTAMP function. This function converts a date value into a UNIX timestamp.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
BIGINT UNIX_TIMESTAMP(VARCHAR date)
BIGINT UNIX_TIMESTAMP(TIMESTAMP timestamp)
BIGINT UNIX_TIMESTAMP(VARCHAR date, VARCHAR format)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|timestamp|TIMESTAMP|N/A.|
|date|VARCHAR|The default date format is `yyyy-MM-dd HH:mm:ss`.|
|format|VARCHAR|The default date format is `yyyy-MM-dd hh:mm:ss`.|

## Example

-   Test data

    |date1\(VARCHAR\)|date2\(VARCHAR\)|
    |----------------|----------------|
    |2021-03-25 00:00:00|1970-01-01 00:00:00|

-   Test statements

    ```
    SELECT UNIX_TIMESTAMP(to_timestamp_tz(date1)) as big1,
           UNIX_TIMESTAMP(date2) as big2
    FROM T1;
    ```

-   Test result

    |big1\(BIGINT\)|big2 \(BIGINT\)|
    |--------------|---------------|
    |1616601600|-28800|


