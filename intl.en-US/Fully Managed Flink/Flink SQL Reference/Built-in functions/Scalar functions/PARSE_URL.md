# PARSE\_URL

The topic describes how to use the PARSE\_URL function. This function parses a specific part of a URL and returns the obtained value.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR PARSE_URL(VARCHAR urlStr, VARCHAR partToExtract [, VARCHAR key])
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|urlStr|VARCHAR|The URL that you want to parse. **Note:** If the value of urlStr is NULL, NULL is returned. |
|partToExtract|VARCHAR|The part that you want to parse from the URL. Valid values: -   HOST
-   PATH
-   QUERY
-   REF
-   PROTOCOL
-   FILE
-   AUTHORITY
-   USERINFO |
|key|VARCHAR|The name of the key whose value you want to extract. This parameter is optional.|

## Example

-   Test data

    |url1\(VARCHAR\)|nullstr\(VARCHAR\)|
    |---------------|------------------|
    |http://facebook.com/path/p1.php?query=1|NULL|

-   Test statements

    ```
    SELECT PARSE_URL(url1, 'QUERY', 'query') as var1,
           PARSE_URL(url1, 'QUERY') as var2,
           PARSE_URL(url1, 'HOST') as var3,
           PARSE_URL(url1, 'PATH') as var4,
           PARSE_URL(url1, 'REF') as var5,
           PARSE_URL(url1, 'PROTOCOL') as var6,
           PARSE_URL(url1, 'FILE') as var7,
           PARSE_URL(url1, 'AUTHORITY') as var8,
           PARSE_URL(nullstr, 'QUERY') as var9,
           PARSE_URL(url1, 'USERINFO') as var10,
           PARSE_URL(nullstr, 'QUERY', 'query') as var11
    FROM T1; 
    ```

-   Test result

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|var4\(VARCHAR\)|var5\(VARCHAR\)|var6\(VARCHAR\)|var7\(VARCHAR\)|var8\(VARCHAR\)|var9\(VARCHAR\)|var10\(VARCHAR\)|var11\(VARCHAR\)|
    |---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|----------------|----------------|
    |1|query=1|facebook.com|/path/p1.php|NULL|http|/path/p1.php?query=1|facebook.com|NULL|NULL|NULL|


