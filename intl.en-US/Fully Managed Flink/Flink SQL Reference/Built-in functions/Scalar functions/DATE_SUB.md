# DATE\_SUB

The topic describes how to use the DATE\_SUB function. This function subtracts specific days from a date and then returns the obtained date.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR DATE_SUB(VARCHAR startdate, INT days)
VARCHAR DATE_SUB(TIMESTAMP time, INT days)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|startdate|VARCHAR **Note:** A date of the VARCHAR type is in the yyyy-MM-dd or yyyy-MM-dd HH:mm:ss format. |
|time|TIMESTAMP|
|days|INT|

**Note:** If one of the input parameters is NULL or a parsing error occurs, the return value is NULL.

## Example

-   Test data

    |date1\(VARCHAR\)|nullstr\(VARCHAR\)|
    |----------------|------------------|
    |2017-10-15|NULL|

-   Test statements

    ```
    SELECT DATE_SUB(date1, 30) as var1,
     DATE_SUB(TIMESTAMP '2017-10-15 23:00:00',30) as var2,
     DATE_SUB(nullstr,30) as var3
    FROM T1;
    ```

-   Test result

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-09-15|2017-09-15|NULL|


