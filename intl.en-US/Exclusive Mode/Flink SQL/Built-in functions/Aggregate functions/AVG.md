# AVG

This topic describes how to use the aggregate function AVG in Realtime Compute for Apache Flink. In Flink SQL, the AVG function returns the average value of all values in a specified expression.

## Syntax

```
AVG(A)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|A|TINYINT, SMALLINT, INT, BIGINT, FLOAT, DECIMAL, or DOUBLE|

## Description

Returns the average value of all the values in a specified expression.

**Note:** The return value is of the DOUBLE type by default. If the field value in the result table is of a type other than DOUBLE, you must use [CAST](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/Type conversion function/CAST.md) to convert the data type.

## Example

-   Test data

    |var1\(INT\)|var2\(INT\)|
    |-----------|-----------|
    |4|30|
    |6|30|

-   Test statements

    ```
    SELECT AVG(var1) as aa
    FROM T1;     
    ```

-   Test results

    |aa\(INT\)|
    |---------|
    |5|


