# DATEDIFF

The topic describes how to use the DATEDIFF function. This function calculates the difference between two date values.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
INT DATEDIFF(VARCHAR enddate, VARCHAR startdate)
INT DATEDIFF(TIMESTAMP enddate, VARCHAR startdate)
INT DATEDIFF(VARCHAR enddate, TIMESTAMP startdate)
INT DATEDIFF(TIMESTAMP enddate, TIMESTAMP startdate)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|startdate|TIMESTAMP or VARCHAR|
|enddate|TIMESTAMP or VARCHAR|

**Note:**

-   A date of the VARCHAR type is in the yyyy-MM-dd or yyyy-MM-dd HH:mm:ss format.
-   If an input parameter is NULL or a parsing error occurs, NULL is returned.

## Example

-   Test data

    |datetime1\(VARCHAR\)|datetime2\(VARCHAR\)|nullstr\(VARCHAR\)|
    |--------------------|--------------------|------------------|
    |2017-10-15 00:00:00|2017-09-15 00:00:00|NULL|

-   Test statements

    ```
    SELECT  DATEDIFF(datetime1, datetime2) as int1, 
            DATEDIFF(TIMESTAMP '2017-10-15 23:00:00',datetime2) as int2, 
            DATEDIFF(datetime2,TIMESTAMP '2017-10-15 23:00:00') as int3, 
            DATEDIFF(datetime2,nullstr) as int4, 
            DATEDIFF(nullstr,TIMESTAMP '2017-10-15 23:00:00') as int5, 
            DATEDIFF(nullstr,datetime2) as int6, 
            DATEDIFF(TIMESTAMP '2017-10-15 23:00:00',TIMESTAMP '2017-9-15 00:00:00')as int7
    FROM T1;
    ```

-   Test result

    |int1\(INT\)|int2\(INT\)|int3\(INT\)|int4\(INT\)|int5\(INT\)|int6\(INT\)|int7\(INT\)|
    |-----------|-----------|-----------|-----------|-----------|-----------|-----------|
    |30|31|-31|NULL|NULL|NULL|31|


