# FROM\_UNIXTIME

This topic describes how to use the FROM\_UNIXTIME function. This function returns a date of the VARCHAR type.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR FROM_UNIXTIME(BIGINT unixtime, VARCHAR format)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|unixtime|BIGINT|The timestamp. Unit: seconds.|
|format|VARCHAR|The format of logs. This parameter is optional. The default format is yyyy-MM-dd HH:mm:ss.|

**Note:** If an input parameter is NULL or a parsing error occurs, NULL is returned.

## Example

-   Test data

    |unixtime1\(BIGINT\)|nullstr\(VARCHAR\)|
    |-------------------|------------------|
    |1505404800|NULL|

-   Test statements

    ```
    SELECT FROM_UNIXTIME(unixtime1) as var1, 
     FROM_UNIXTIME(unixtime1,'MMdd-yyyy') as var2,
     FROM_UNIXTIME(unixtime1,nullstr) as var3
    FROM T1;               
    ```

-   Test result

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-09-15 00:00:00|0915-2017|NULL|


