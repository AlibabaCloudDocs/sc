# FIRST\_VALUE

本文为您介绍如何使用FIRST\_VALUE函数，返回指定数据流的第1条非NULL数据。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
FIRST_VALUE( T value )
FIRST_VALUE( T value, BIGINT order )
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|value|任意参数类型|指定数据流。**说明：** 如果输入数据全为NULL，则返回NULL。 |
|order|BIGINT|可选，根据order判定FIRST\_VALUE所在行，取order值最小的记录作为FIRST\_VALUE。如果没有填写该参数，则按系统。 |

**说明：** 所有输入参数需要为相同的数据类型。

## 示例

-   测试数据

    |a\(BIGINT\)|b\(INT\)|c\(VARCHAR\)|
    |-----------|--------|------------|
    |1|1|Hello|
    |2|2|Hello|
    |3|3|Hello|
    |4|4|Hello|
    |5|5|Hello|
    |6|6|Hello|
    |7|7|NULL|
    |8|7|Hello World|
    |9|8|Hello World|
    |10|20|Hello World|

-   测试语句

    ```
    SELECT c,FIRST_VALUE(b) 
    OVER (PARTITION BY c ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING) AS var1
    FROM T1;
    ```

-   测试结果

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |Hello|1|
    |Hello|1|
    |Hello|1|
    |Hello|1|
    |Hello|1|
    |Hello|1|
    |NULL|7|
    |Hello World|7|
    |Hello World|7|
    |Hello World|7|


