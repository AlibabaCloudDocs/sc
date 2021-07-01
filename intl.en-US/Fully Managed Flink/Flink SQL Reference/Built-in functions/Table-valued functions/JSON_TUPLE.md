# JSON\_TUPLE

This topic describes how to use the JSON\_TUPLE function. This function retrieves the values that are represented by each path string from a JSON string.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
JSON_TUPLE(str, path1, path2 ..., pathN)     
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The JSON string.|
|path1 to pathN|VARCHAR|A path string, which does not start with a dollar sign \(`$`\).|

## Example

-   Test data

    |d\(VARCHAR\)|s\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|qwe3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|qwe2|

-   Test statements

    ```
    SELECT d, v 
    FROM T1, lateral table(JSON_TUPLE(d, 'qwe', s))
    AS T(v);   
    ```

-   Test result

    |d\(VARCHAR\)|v\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd4|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd5|


