# REGEXP

This topic describes how to use the REGEXP function. This function searches a specified string by using a regular expression and returns a Boolean value based on the matching result.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
BOOLEAN REGEXP(VARCHAR str, VARCHAR pattern) 
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The specified string.|
|pattern|VARCHAR|The specified pattern.|

**Note:** If the string or pattern parameter is empty or NULL, false is returned.

## Example

-   Test data

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|
    |---------------|-------------------|
    |k1=v1;k2=v2|k2\*|
    |k1:v1\|k2:v2|k3|
    |NULL|k3|
    |k1:v1\|k2:v2|NULL|
    |k1:v1\|k2:v2|\(|

-   Test statements

    ```
    SELECT  REGEXP(str1, pattern1) AS result
    FROM T1;              
    ```

-   Test result

    |result\(BOOLEAN\)|
    |-----------------|
    |true|
    |false|
    |false|
    |false|
    |false|


