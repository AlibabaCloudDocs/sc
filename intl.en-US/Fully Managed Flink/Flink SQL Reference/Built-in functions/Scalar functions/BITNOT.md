# BITNOT

This topic describes how to use the BITNOT function to perform a bitwise NOT operation.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
INT BITNOT(INT number)     
```

**Note:** The input and output parameters are both of the INT type.

## Input parameters

|Parameter|Data type|
|---------|---------|
|number|INT|

## Example

-   Test data

    |a\(INT\)|
    |--------|
    |7|

-   Test statements

    ```
    SELECT BITNOT(a) as var1
    FROM T1;             
    ```

-   Test result

    |var1\(INT\)|
    |-----------|
    |-8|


