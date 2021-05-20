# IS NOT NULL

本文为您介绍如何使用实时计算逻辑运算函数IS NOT NULL。

## 语法

```
value IS NOT NULL
```

## 入参

|参数|数据类型|
|--|----|
|value|任意数据类型|

## 功能描述

如果value为`NULL`时，则返回`FALSE`，否则返回`TRUE`。

## 示例

-   测试数据

    |int1\(INT\)|int2\(VARCHAR\)|
    |-----------|---------------|
    |97|NULL|
    |9|ww123|

-   测试语句

    ```
    SELECT int1 as aa
    FROM T1
    WHERE int2 IS NOT NULL;            
    ```

-   测试结果

    |aa\(int\)|
    |---------|
    |9|


