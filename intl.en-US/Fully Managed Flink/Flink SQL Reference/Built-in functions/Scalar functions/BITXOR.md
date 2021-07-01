# BITXOR

This topic describes how to use the BITXOR function to perform a bitwise XOR operation.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
INT BITXOR(INT number1, INT number2)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|number1|INT|
|number2|INT|

**Note:** The input and output parameters are both of the INT type.

## Example

-   Test data

    |a\(INT\)|b\(INT\)|
    |--------|--------|
    |2|3|

-   Test statements

    ```
    SELECT BITXOR(a, b) as var1
    FROM T1;               
    ```

-   Test result

    |var1\(INT\)|
    |-----------|
    |1|


