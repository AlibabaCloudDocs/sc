# DATE\_FORMAT\_TZ

This topic describes how to use the DATE\_FORMAT\_TZ function. This function converts data of the timestamp type into a string of the specified type based on the specified time zone.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR DATE_FORMAT_TZ(TIMESTAMP timestamp, VARCHAR format, VARCHAR timezone)
VARCHAR DATE_FORMAT_TZ(TIMESTAMP timestamp, VARCHAR timezone)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|timestamp|TIMESTAMP|The data type of time.|
|format|VARCHAR|The format of the output date, for example, yyyy-MM-dd HH:mm:ss.|
|timezone|VARCHAR|The time zone, for example, Asia/Shanghai.|

## Example

-   Test data

    |timestamp1\(TIMESTAMP\)|format1\(VARCHAR\)|timezone1\(VARCHAR\)|
    |-----------------------|------------------|--------------------|
    |0|yyyy-MM-dd HH:mm:ss|Asia/Shanghai|

-   Test statements

    ```
    SELECT 
       DATE_FORMAT_TZ(timestamp1, format1, timezone1) as var1,
       DATE_FORMAT_TZ(timestamp1, timezone1) as var2
    FROM T1;
    ```

-   Test result

    |var1\(VARCHAR\)|var2\(VARCHAR\)|
    |---------------|---------------|
    |1970-01-01 08:00:00|1970-01-01 08:00:00|


