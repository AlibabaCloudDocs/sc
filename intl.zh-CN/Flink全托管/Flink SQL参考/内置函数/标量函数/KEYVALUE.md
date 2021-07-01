# KEYVALUE

本文为您介绍如何使用KEYVALUE函数，将目标字符串按照split1分隔符拆分成Key-Value（KV）对，然后按照split2分隔符将Key-Value对分开，返回Key对应的Value。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR KEYVALUE(VARCHAR str, VARCHAR split1, VARCHAR split2, VARCHAR key_name)      
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|待拆分的目标字符串。|
|split1|VARCHAR|按照split1分隔符，将目标字符串拆分成Key-Value（KV）对。|
|split2|VARCHAR|按照split2分隔符将Key-Value对分开。|
|key\_name|VARCHAR|Key的名称。|

**说明：**

-   如果split1或split2为NULL时，则返回NULL。
-   如果str或者key\_name为NULL时，则返回NULL。
-   如果没有匹配的Key时，则返回NULL。
-   如果有多个key-value匹配，返回第一个匹配上的Key对应的Value。

## 示例

-   测试数据

    |str\(VARCHAR\)|split1\(VARCHAR\)|split2\(VARCHAR\)|key1\(VARCHAR\)|
    |--------------|-----------------|-----------------|---------------|
    |k1=v1;k2=v2|;|=|k2|
    |NULL|;|\||:|
    |k1:v1\|k2:v2|NULL|=|:|
    |k1:v1\|k2:v2|\||=|NULL|
    |k1:v1\|k2:v2|\||=|:|

-   测试语句

    ```
    SELECT  KEYVALUE(str, split1, split2, key1) as `result`
    FROM T1;         
    ```

-   测试结果

    |result\(VARCHAR\)|
    |-----------------|
    |v2|
    |NULL|
    |NULL|
    |NULL|
    |NULL|


