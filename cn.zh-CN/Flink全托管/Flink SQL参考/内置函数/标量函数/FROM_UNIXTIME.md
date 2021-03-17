# FROM\_UNIXTIME

本文为您介绍如何使用FROM\_UNIXTIME函数，返回VARCHAR类型的日期值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR FROM_UNIXTIME(BIGINT unixtime, VARCHAR format)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|unixtime|BIGINT|以秒为单位的时间戳。|
|format|VARCHAR|可选，日志格式，默认格式为yyyy-MM-dd HH:mm:ss。|

**说明：** 如果入参中任何一个为NULL或解析错误，则返回为NULL。

## 示例

-   测试数据

    |unixtime1\(BIGINT\)|nullstr\(VARCHAR\)|
    |-------------------|------------------|
    |1505404800|NULL|

-   测试语句

    ```
    SELECT FROM_UNIXTIME(unixtime1) as var1, 
     FROM_UNIXTIME(unixtime1,'MMdd-yyyy') as var2,
     FROM_UNIXTIME(unixtime1,nullstr) as var3
    FROM T1;               
    ```

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-09-15 00:00:00|0915-2017|NULL|


