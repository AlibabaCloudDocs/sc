# KEYVALUE

This topic describes how to use the KEYVALUE function. This function splits a string into key-value pairs based on the split1 delimiter, and splits the key-value pairs based on the split2 delimiter. The value that corresponds to the key is returned.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR KEYVALUE(VARCHAR str, VARCHAR split1, VARCHAR split2, VARCHAR key_name)      
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The string that you want to split.|
|split1|VARCHAR|The split1 delimiter that splits the specified string into key-value pairs.|
|split2|VARCHAR|The split2 delimiter that splits the key-value pairs.|
|key\_name|VARCHAR|The name of the key.|

**Note:**

-   If the split1 or split2 parameter is NULL, NULL is returned.
-   If the str or key\_name parameter is NULL, NULL is returned.
-   If no key is found, NULL is returned.
-   If multiple key-value pairs exist, the value that first matches the key is returned.

## Example

-   Test data

    |str\(VARCHAR\)|split1\(VARCHAR\)|split2\(VARCHAR\)|key1\(VARCHAR\)|
    |--------------|-----------------|-----------------|---------------|
    |k1=v1;k2=v2|;|=|k2|
    |NULL|;|\||:|
    |k1:v1\|k2:v2|NULL|=|:|
    |k1:v1\|k2:v2|\||=|NULL|
    |k1:v1\|k2:v2|\||=|:|

-   Test statements

    ```
    SELECT  KEYVALUE(str, split1, split2, key1) as `result`
    FROM T1;         
    ```

-   Test result

    |result\(VARCHAR\)|
    |-----------------|
    |v2|
    |NULL|
    |NULL|
    |NULL|
    |NULL|


