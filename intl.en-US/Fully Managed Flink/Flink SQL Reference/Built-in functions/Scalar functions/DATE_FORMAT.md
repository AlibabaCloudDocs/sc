# DATE\_FORMAT

This topic describes how to use the DATE\_FORMAT function. This function converts a date value of the STRING type from a format into a specific format.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR DATE_FORMAT(TIMESTAMP time, VARCHAR to_format)
VARCHAR DATE_FORMAT(VARCHAR date, VARCHAR to_format)
VARCHAR DATE_FORMAT(VARCHAR date, VARCHAR from_format, VARCHAR to_format)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|time|TIMESTAMP|The source string.|
|date|VARCHAR|The source string. The default format is yyyy-MM-dd HH:mm:ss.|
|from\_format|VARCHAR|The original format. This parameter is optional. The default format is yyyy-MM-dd hh:mm:ss.|
|to\_format|VARCHAR|The format of the output date.|

**Note:** If an input parameter is NULL or a parsing error occurs, NULL is returned.

## Example

-   Test data

    |date1\(VARCHAR\)|datetime1\(VARCHAR\)|nullstr\(VARCHAR\)|
    |----------------|--------------------|------------------|
    |0915-2017|2017-09-15 00:00:00|NULL|

-   Test statements

    ```
    SELECT DATE_FORMAT(datetime1, 'yyMMdd') as var1,
     DATE_FORMAT(nullstr, 'yyMMdd') as var2,
     DATE_FORMAT(datetime1, nullstr) as var3,
     DATE_FORMAT(date1, 'MMdd-yyyy', nullstr) as var4,
     DATE_FORMAT(date1, 'MMdd-yyyy', 'yyyyMMdd') as var5,
     DATE_FORMAT(TIMESTAMP '2017-09-15 23:00:00', 'yyMMdd') as var6
    FROM T1;
    ```

-   Test result

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|var4\(VARCHAR\)|var5\(VARCHAR\)|var6\(VARCHAR\)|
    |---------------|---------------|---------------|---------------|---------------|---------------|
    |170915|null|null|null|20170915|170915|


