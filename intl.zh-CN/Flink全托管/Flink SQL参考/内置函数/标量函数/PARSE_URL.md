# PARSE\_URL

本文为您介绍如何使用PARSE\_URL函数，返回目标URL中指定部分解析后的值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR PARSE_URL(VARCHAR urlStr, VARCHAR partToExtract [, VARCHAR key])
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|urlStr|VARCHAR|目标URL。**说明：** 如果urlStr参数值为NULL时，则返回值为NULL。 |
|partToExtract|VARCHAR|指定链接中解析的部分。取值如下： -   HOST
-   PATH
-   QUERY
-   REF
-   PROTOCOL
-   FILE
-   AUTHORITY
-   USERINFO |
|key|VARCHAR|可选，指定截取部分，具体的键。|

## 示例

-   测试数据

    |url1\(VARCHAR\)|nullstr\(VARCHAR\)|
    |---------------|------------------|
    |http://facebook.com/path/p1.php?query=1|NULL|

-   测试语句

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

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|var4\(VARCHAR\)|var5\(VARCHAR\)|var6\(VARCHAR\)|var7\(VARCHAR\)|var8\(VARCHAR\)|var9\(VARCHAR\)|var10\(VARCHAR\)|var11\(VARCHAR\)|
    |---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|----------------|----------------|
    |1|query=1|facebook.com|/path/p1.php|NULL|http|/path/p1.php?query=1|facebook.com|NULL|NULL|NULL|


