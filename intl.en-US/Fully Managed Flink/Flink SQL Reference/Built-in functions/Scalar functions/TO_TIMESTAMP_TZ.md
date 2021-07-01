# TO\_TIMESTAMP\_TZ

This topic describes how to use the TO\_TIMESTAMP\_TZ function. This function converts a date value of the VARCHAR type into the TIMESTAMP type based on the specified time zone.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
TIMESTAMP TO_TIMESTAMP_TZ(VARCHAR date, VARCHAR timezone)
TIMESTAMP TO_TIMESTAMP_TZ(VARCHAR date, VARCHAR format, VARCHAR timezone)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|timezone|VARCHAR|The time zone, for example, Asia/Shanghai.|
|date|VARCHAR|The default format is yyyy-MM-dd HH:mm:ss.|
|format|VARCHAR|The date format.|

## Example

-   Test data

    |timestamp1\(VARCHAR\)|format1\(VARCHAR\)|timezone1\(VARCHAR\)|
    |---------------------|------------------|--------------------|
    |1970-01-01 00:00:00|yyyy-MM-dd HH:mm:ss|Asia/Shanghai|

-   Test statements

    ```
    SELECT  
         UNIX_TIMESTAMP(TO_TIMESTAMP_TZ(timestamp1, timezone1)) as var1,
         UNIX_TIMESTAMP(TO_TIMESTAMP_TZ(timestamp1, format1, timezone1)) as var2   
    FROM T1;
    ```

-   Test result

    |var1\(BIGINT\)|var2\(BIGINT\)|
    |--------------|--------------|
    |-28800|-28800|


