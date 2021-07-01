# REGEXP

本文为您介绍如何使用REGEXP函数，对指定的字符串执行一个正则表达式搜索，并返回一个Boolean值表示是否找到指定的匹配模式。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
BOOLEAN REGEXP(VARCHAR str, VARCHAR pattern) 
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|指定的匹配模式。|

**说明：** 如果str或pattern为空或为NULL，则返回false。

## 示例

-   测试数据

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|
    |---------------|-------------------|
    |k1=v1;k2=v2|k2\*|
    |k1:v1\|k2:v2|k3|
    |NULL|k3|
    |k1:v1\|k2:v2|NULL|
    |k1:v1\|k2:v2|\(|

-   测试语句

    ```
    SELECT  REGEXP(str1, pattern1) AS result
    FROM T1;              
    ```

-   测试结果

    |result\(BOOLEAN\)|
    |-----------------|
    |true|
    |false|
    |false|
    |false|
    |false|


