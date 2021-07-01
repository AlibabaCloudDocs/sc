# SPLIT\_INDEX

This topic describes how to use the SPLIT\_INDEX function. This function splits a string into several segments based on the specified delimiter and returns the field at the specified position.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR SPLIT_INDEX(VARCHAR str, VARCHAR sep, INT index)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The string that you want to split.|
|sep|VARCHAR|The delimiter.|
|index|INT|The field position. The index starts from 0. **Note:** If no field is obtained, NULL is returned. |

**Note:** If an input parameter is set to NULL, NULL is returned.

## Example

-   Test data

    |str\(VARCHAR\)|sep\(VARCHAR\)|index\(INT\)|
    |--------------|--------------|------------|
    |Jack,John,Mary|,|2|
    |Jack,John,Mary|,|3|
    |Jack,John,Mary|NULL|0|
    |NULL|,|0|

-   Test statements

    ```
    SELECT  SPLIT_INDEX(str, sep, index) as var1
    FROM T1;         
    ```

-   Test result

    |var1\(VARCHAR\)|
    |---------------|
    |Mary|
    |NULL|
    |NULL|
    |NULL|


