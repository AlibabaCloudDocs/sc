# MULTI\_KEYVALUE

This topic describes how to use the MULTI\_KEYVALUE function. This function splits a string into key-value pairs based on the key-value pair delimiter specified by split1, splits a key-value pair based on the delimiter specified by split2, and returns the values of key\_name1 and key\_name2.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
MULTI_KEYVALUE(VARCHAR str, VARCHAR split1, VARCHAR split2, VARCHAR key_name1, VARCHAR key_name2, ...)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The string that you want to split.|
|split1|VARCHAR|The key-value pair delimiter. If the value of split1 is NULL, spaces are used as key-value pair delimiters. If the length of split1 is greater than 1, split1 represents only a set of delimiters, in which each character represents a valid delimiter. |
|split2|VARCHAR|The key-value delimiter. If the value of split2 is NULL, spaces are used as key-value delimiters. If the length of split2 is greater than 1, split2 represents only a set of delimiters, in which each character represents a valid delimiter. |
|key\_name1, key\_name2, ...|VARCHAR|The list of keys whose values you want to obtain.|

## Example

-   Test data

    |str\(VARCHAR\)|split1\(VARCHAR\)|split2\(VARCHAR\)|key1\(VARCHAR\)|key2\(VARCHAR\)|
    |--------------|-----------------|-----------------|---------------|---------------|
    |k1=v1;k2=v2|;|=|k1|k2|
    |NULL|;|=|k1|k2|
    |k1:v1;k2:v2|;|:|k1|k3|
    |k1:v1;k2:v2|;|=|k1|k2|
    |k1:v1;k2:v2|,|:|k1|k2|
    |k1:v1;k2=v2|;|:|k1|k2|
    |k1:v1abck2:v2|abc|:|k1|k2|
    |k1:v1;k2=v2|;|:=|k1|k2|
    |k1:v1 k2:v2|NULL|:|k1|k2|
    |k1 v1;k2 v2|;|NULL|k1|k2|

-   Test statements

    ```
    SELECT c1, c2 
    FROM T1, lateral table(MULTI_KEYVALUE(str, split1, split2, key1, key2)) 
    as T(c1, c2);                
    ```

-   Test result

    |c1\(VARCHAR\)|c2\(VARCHAR\)|
    |-------------|-------------|
    |v1|v2|
    |NULL|NULL|
    |v1|NULL|
    |NULL|NULL|
    |NULL|NULL|
    |v1|NULL|
    |v1|v2|
    |v1|v2|
    |v1|v2|
    |v1|v2|


