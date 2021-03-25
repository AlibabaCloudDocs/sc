# MULTI\_KEYVALUE

本文为您介绍如何使用MULTI\_KEYVALUE函数，将目标字符串中拆分为Key-Value（KV）对，匹配split1和split2的KV对，并返回key\_name1和key\_name2对应的Value值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
MULTI_KEYVALUE(VARCHAR str, VARCHAR split1, VARCHAR split2, VARCHAR key_name1, VARCHAR key_name2, ...)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|目标字符串。|
|split1|VARCHAR|KV对的分隔符。当split1为NULL时，表示按照空格作为KV对的分割符。当split1的长度\>1时，split1仅表示分隔符的集合，每个字符都表示一个有效的分隔符。 |
|split2|VARCHAR|KV的分隔符。当split2为NULL时，表示按照空格作为KV的分割符。当split2的长度\>1时，split2仅表示分隔符的集合，每个字符都表示一个有效的分隔符。 |
|key\_name1, key\_name2, ...|VARCHAR|需要获取Value的Key值列表。|

## 示例

-   测试数据

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

-   测试语句

    ```
    SELECT c1, c2 
    FROM T1, lateral table(MULTI_KEYVALUE(str, split1, split2, key1, key2)) 
    as T(c1, c2);                
    ```

-   测试结果

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


