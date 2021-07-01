# CONV

This topic describes how to use the CONV function. This function performs base conversation on numeric values or characters.

**Note:**

-   This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.
-   The CONV\(\) function works with the 64-bit precision.

## Syntax

```
VARCHAR CONV(BIGINT number, INT FROM_BASE, INT TO_BASE)
or 
VARCHAR CONV(VARCHAR number, INT FROM_BASE, INT TO_BASE)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|number|BIGINT or VARCHAR|**Note:** If the value of the number parameter is NULL or an invalid character, NULL is returned. |
|FROM\_BASE|INT|Valid values: \[2, 36\].|
|TO\_BASE|INT|Positive valid values: \[2, 36\]. Negative valid values: \[-36, -2\].|

## Example

-   Test data

    |id\(INT\)|x\(BIGINT\)|y \(VARCHAR\)|
    |---------|-----------|-------------|
    |1|12L|'12'|
    |2|10L|'10'|
    |3|0L|'test'|
    |4|NULL|NULL|

-   Test statements

    ```
    SELECT  id, conv(x, 10, 16) as var1, conv(y, 10, 2) as var2
    FROM T1;
    ```

-   Test result

    |id\(INT\)|var1\(VARCHAR\)|var2\(VARCHAR\)|
    |---------|---------------|---------------|
    |1|C|1100|
    |2|A|1010|
    |3|0|NULL|
    |4|NULL|NULL|


