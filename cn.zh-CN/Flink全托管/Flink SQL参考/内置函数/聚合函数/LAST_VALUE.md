# LAST\_VALUE

本文为您介绍如何使用LAST\_VALUE函数，返回指定数据流的最后1条非NULL数据。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
T LAST_VALUE(T value)
T LAST_VALUE(T value，BIGINT order)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|value|任意参数类型|指定数据流。|
|order|BIGINT|根据order判定LAST\_VALUE所在的行，取order值最大的记录作为LAST\_VALUE。|

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
    SELECT c,LAST_VALUE(b) 
    OVER (PARTITION BY c ORDER BY PROCTIME() RANGE UNBOUNDED PRECEDING) AS var1
    FROM T1;
    ```

-   测试结果

    |c\(VARCHAR\)|var1\(INT\)|
    |------------|-----------|
    |Hello|1|
    |Hello|2|
    |Hello|3|
    |Hello|4|
    |Hello|5|
    |Hello|6|
    |NULL|7|
    |Hello World|7|
    |Hello World|8|
    |Hello World|20|


