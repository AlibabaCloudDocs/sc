# BITAND

This topic describes how to use the BITAND function to perform a bitwise AND operation.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
INT BITAND(INT number1,INT number2)        
```

**Note:** The input and output parameters are both of the INT type.

## Input parameters

|Parameter|Data type|
|---------|---------|
|number1|INT|
|number2|INT|

## Example

-   Test data

    |a\(INT\)|b\(INT\)|
    |--------|--------|
    |2|3|

-   Test statements

    ```
    SELECT BITAND(a, b) as intt
    FROM T1;              
    ```

-   Test result

    |intt\(INT\)|
    |-----------|
    |2|


