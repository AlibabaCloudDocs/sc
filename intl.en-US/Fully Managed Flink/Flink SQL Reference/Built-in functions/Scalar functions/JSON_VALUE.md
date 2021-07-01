# JSON\_VALUE

This topic describes how to use the JSON\_VALUE function. This function extracts the expression of the specified path from a JSON string.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR JSON_VALUE(VARCHAR content, VARCHAR path)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|content|VARCHAR|The JSON object that you want to parse.|
|path|VARCHAR|The expression of the specified path. You must use single quotation marks \('\) in a custom path. Example: ```
JSON_VALUE(json,'$.passenger_name') AS ABC
```

The following symbols are supported in a path:-   $: indicates the root object.
-   \[\]: indicates the array subscript.
-   \*: indicates the array wildcard characters.
-   .: indicates child elements. |

**Note:** If the input parameters contain invalid JSON or NULL values, NULL is returned.

## Example

-   Test data

    |id\(INT\)|json\(VARCHAR\)|path1\(VARCHAR\)|
    |---------|---------------|----------------|
    |1|\[10, 20, \[30, 40\]\]|$\[2\]\[\*\]|
    |2|\{"aaa":"bbb","ccc":\{"ddd":"eee","fff":"ggg","hhh":\["h0","h1","h2"\]\},"iii":"jjj"\}|$.ccc.hhh\[\*\]|
    |3|\{"aaa":"bbb","ccc":\{"ddd":"eee","fff":"ggg",hhh":\["h0","h1","h2"\]\},"iii":"jjj"\}|$.ccc.hhh\[1\]|
    |4|\[10, 20, \[30, 40\]\]|NULL|
    |5|NULL|$\[2\]\[\*\]|
    |6|"\{xx\]"|"$\[2\]\[\*\]"|

-   Test statements

    ```
    SELECT 
        id, 
        JSON_VALUE(json, path1) AS `value`
    FROM 
        T1;
    ```

-   Test result

    |id \(INT\)|value \(VARCHAR\)|
    |----------|-----------------|
    |1|\[30,40\]|
    |2|\["h0","h1","h2"\]|
    |3|H1|
    |4|NULL|
    |5|NULL|
    |6|NULL|


