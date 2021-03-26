# MURMUR\_HASH\_64

本文为您介绍如何使用MURMUR\_HASH\_64函数，按Murmur 128哈希算法计算指定参数的64位哈希值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
BIGINT MURMUR_HASH_64(value)
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
    SELECT  MURMUR_HASH_64(value) as big
    FROM T1;         
    ```

-   测试结果

    |big\(VARCHAR\)|
    |--------------|
    |2706826604463634835|
    |NULL|
    |5010266691562018262|


## 示例2

-   测试数据

    |value|
    |-----|
    |4.5|
    |NULL|
    |4|

-   测试语句

    ```
    SELECT MURMUR_HASH_64(CAST(value AS FLOAT)) as big
    FROM T2;         
    ```

-   测试结果

    |big\(VARCHAR\)|
    |--------------|
    |8033179276572534397|
    |NULL|
    |-6697622088025985219|


