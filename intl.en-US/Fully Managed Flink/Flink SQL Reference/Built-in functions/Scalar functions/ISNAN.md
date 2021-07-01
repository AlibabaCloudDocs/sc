# ISNAN

This topic describes how to use the ISNAN function. This function checks whether the specified floating-point number is NAN \(Not a number\). This function is similar to the Double.isNan\(\) or Float.isNan\(\) function in Java.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
BOOLEAN ISNAN(DOUBLE d)
BOOLEAN ISNAN(FLOAT f)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|d|DOUBLE|
|f|FLOAT|

## Example

-   Test data

    |d|f|
    |--|--|
    |0.1|0.1|
    |0|0|

-   Test statements

    ```
    SELECT  
    ISNAN(CAST(d AS DOUBLE)) as d1,
    ISNAN(CAST(f AS FLOAT)) as d2
    FROM T1;         
    ```

-   Test result

    |d1\(BOOLEAN\)|d2\(BOOLEAN\)|
    |-------------|-------------|
    |false|false|
    |false|false|


