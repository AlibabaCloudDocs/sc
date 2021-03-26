# ISNAN

本文为您介绍如何使用ISNAN函数，判断指定的浮点数是否为NaN（Not a number），类似于Java中Double.isNaN\(\)或Float.isNaN\(\)函数。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
BOOLEAN ISNAN(DOUBLE d)
BOOLEAN ISNAN(FLOAT f)
```

## 入参

|参数|数据类型|
|--|----|
|d|DOUBLE|
|f|FLOAT|

## 示例

-   测试数据

    |d|f|
    |--|--|
    |0.1|0.1|
    |0|0|

-   测试语句

    ```
    SELECT  
    ISNAN(CAST(d AS DOUBLE)) as d1,
    ISNAN(CAST(f AS FLOAT)) as d2
    FROM T1;         
    ```

-   测试结果

    |d1\(BOOLEAN\)|d2\(BOOLEAN\)|
    |-------------|-------------|
    |false|false|
    |false|false|


