# BITAND

本文为您介绍如何使用BITAND函数进行按位与操作。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
INT BITAND(INT number1,INT number2)        
```

**说明：** 输入和输出类型均为整型，且类型一致。

## 入参

|参数|数据类型|
|--|----|
|number1|INT|
|number2|INT|

## 示例

-   测试数据

    |a\(INT\)|b\(INT\)|
    |--------|--------|
    |2|3|

-   测试语句

    ```
    SELECT BITAND(a, b) as intt
    FROM T1;              
    ```

-   测试结果

    |intt\(INT\)|
    |-----------|
    |2|


