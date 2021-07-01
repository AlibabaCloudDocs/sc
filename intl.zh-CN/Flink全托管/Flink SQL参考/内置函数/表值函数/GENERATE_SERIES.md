# GENERATE\_SERIES

本文为您介绍如何使用GENERATE\_SERIES函数，按from、from+1、from+2 … to-1的规则，生成一系列填充数据。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
GENERATE_SERIES(INT from, INT to)
```

## 入参

|参数|数据类型|
|--|----|
|from|INT类型，指定下界，包含下界值。|
|to|INT类型，指定上界，不包含上界值。|

## 示例

-   测试数据

    |s\(INT\)|e\(INT\)|
    |--------|--------|
    |1|3|
    |-2|1|

-   测试语句

    ```
    SELECT s, e, v 
    FROM T1, lateral table(GENERATE_SERIES(s, e)) 
    as v;             
    ```

-   测试结果

    |s\(INT\)|e\(INT\)|v\(INT\)|
    |--------|--------|--------|
    |1|3|1|
    |1|3|2|
    |-2|1|-2|
    |-2|1|-1|
    |-2|1|0|


