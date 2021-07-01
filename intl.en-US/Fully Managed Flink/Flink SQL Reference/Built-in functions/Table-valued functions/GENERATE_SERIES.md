# GENERATE\_SERIES

This topic describes how to use the GENERATE\_SERIES function. This function generates a series of padding data based on the rule of from, from+1, from+2 ... to-1.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
GENERATE_SERIES(INT from, INT to)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|from|The lower boundary of a series of values \(including the lower boundary\). This parameter is of the INT type.|
|to|The upper boundary of a series of values \(excluding the upper boundary\). This parameter is of the INT type.|

## Example

-   Test data

    |s\(INT\)|e\(INT\)|
    |--------|--------|
    |1|3|
    |-2|1|

-   Test statements

    ```
    SELECT s, e, v 
    FROM T1, lateral table(GENERATE_SERIES(s, e)) 
    as v;             
    ```

-   Test result

    |s\(INT\)|e\(INT\)|v\(INT\)|
    |--------|--------|--------|
    |1|3|1|
    |1|3|2|
    |-2|1|-2|
    |-2|1|-1|
    |-2|1|0|


