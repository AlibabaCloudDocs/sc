# TO\_DATE

The topic describes how to use the TO\_DATE function. This function converts a date value from the INT or VARCHAR type to the DATE type.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
Date TO_DATE(INT time)
Date TO_DATE(VARCHAR date)
Date TO_DATE(VARCHAR date,VARCHAR format)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|time|INT|The number of days that have elapsed since the epoch time January 1, 1970, 00:00:00 UTC.|
|date|VARCHAR|The default format is yyyy-MM-dd.|
|format|VARCHAR|The date format.|

## Example

-   Test data

    |date1\(INT\)|date2\(VARCHAR\)|date3\(VARCHAR\)|
    |------------|----------------|----------------|
    |100|2017-09-15|20170915|

-   Test statements

    ```
    SELECT TO_DATE(date1) as var1,
     TO_DATE(date2) as var2,
     TO_DATE(date3,'yyyyMMdd') as var3
    FROM T1;
    ```

-   Test result

    |var1\(DATE\)|var2\(DATE\)|var3\(DATE\)|
    |------------|------------|------------|
    |1970-04-11|2017-09-15|2017-09-15|


