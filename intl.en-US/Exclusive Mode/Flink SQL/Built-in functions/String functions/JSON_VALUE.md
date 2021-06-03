# JSON\_VALUE

This topic describes how to use the string function JSON\_VALUE in Realtime Compute.

## Syntax

```
VARCHAR JSON_VALUE(VARCHAR content, VARCHAR path)
```

## Input parameters

-   content

    The JSON object that you want to parse, which is represented as a string. This parameter is of the VARCHAR type.

-   path

    The path expression that is used to parse the JSON object. This parameter is of the VARCHAR type. The following table lists the supported path expressions.

    |Symbol|Description|
    |------|-----------|
    |$|The root object.|
    |\[\]|The array subscript.|
    |\*|The array wildcard.|
    |.|The child element.|


## Description

Extracts the value of the specific path from a JSON string. If the JSON string is invalid or any input parameter is null, null is returned.

## Example

-   Test data

    |id \(INT\)|json \(VARCHAR\)|path1 \(VARCHAR\)|
    |----------|----------------|-----------------|
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

-   Test results

    |id \(INT\)|value \(VARCHAR\)|
    |----------|-----------------|
    |1|\[30,40\]|
    |2|\["h0","h1","h2"\]|
    |3|H1|
    |4|NULL|
    |5|NULL|
    |6|NULL|


