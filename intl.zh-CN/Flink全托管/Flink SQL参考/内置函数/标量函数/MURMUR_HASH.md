# MURMUR\_HASH

本文为您介绍如何使用MURMUR\_HASH函数，按Murmur 32哈希算法计算指定参数的32位哈希值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
INT MURMUR_HASH(value)
```

## 入参

|参数|数据类型|
|--|----|
|value|VARCHAR或数值类型。|

## 示例1

-   测试数据

    |value|
    |-----|
    |This is a test String.|
    |NULL|
    |test|

-   测试语句

    ```
    SELECT  MURMUR_HASH(value) as intt
    FROM T1;         
    ```

-   测试结果

    |intt\(VARCHAR\)|
    |---------------|
    |740959099|
    |NULL|
    |271330425|


## 示例2

-   测试数据

    |str|
    |---|
    |4.5|
    |NULL|
    |4|

-   测试语句

    ```
    SELECT MURMUR_HASH(CAST(str AS FLOAT)) as intt
    FROM T2;         
    ```

-   测试结果

    |intt\(VARCHAR\)|
    |---------------|
    |-2130121231|
    |NULL|
    |1863595885|


