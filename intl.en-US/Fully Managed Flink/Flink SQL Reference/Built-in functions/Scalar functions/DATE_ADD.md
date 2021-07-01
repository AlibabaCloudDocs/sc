# DATE\_ADD

This topic describes how to use the DATE\_ADD function. This function returns the date after the specified number of days is added to a date.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR DATE_ADD(VARCHAR startdate, INT days)
VARCHAR DATE_ADD(TIMESTAMP time, INT days)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|startdate|TIMESTAMP or VARCHAR **Note:** A date of the VARCHAR type is in the yyyy-MM-dd or yyyy-MM-dd HH:mm:ss format. |
|time|TIMESTAMP|
|days|INT|

**Note:** If an input parameter is NULL or a parsing error occurs, NULL is returned.

## Example

-   Test data

    |datetime1\(VATCHAR\)|nullstr\(VATCHAR\)|
    |--------------------|------------------|
    |2017-09-15 00:00:00|null|

-   Test statements

    ```
    SELECT DATE_ADD(datetime1, 30) as var1,
     DATE_ADD(TIMESTAMP '2017-09-15 23:00:00',30) as var2,
     DATE_ADD(nullstr,30) as var3
    FROM T1;   
    ```

-   Test result

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-10-15|2017-10-15|null|


