# DATE\_FORMAT\_TZ

本文为您介绍如何使用DATE\_FORMAT\_TZ函数，将时间戳类型的数据根据指定的时区，转成指定格式的字符串。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR DATE_FORMAT_TZ(TIMESTAMP timestamp, VARCHAR format, VARCHAR timezone)
VARCHAR DATE_FORMAT_TZ(TIMESTAMP timestamp, VARCHAR timezone)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|timestamp|TIMESTAMP|时间类型。|
|format|VARCHAR|输出日期格式，例如yyyy-MM-dd HH:mm:ss。|
|timezone|VARCHAR|指定时区，例如Asia/Shanghai。|

## 示例

-   测试数据

    |timestamp1\(TIMESTAMP\)|format1\(VARCHAR\)|timezone1\(VARCHAR\)|
    |-----------------------|------------------|--------------------|
    |0|yyyy-MM-dd HH:mm:ss|Asia/Shanghai|

-   测试语句

    ```
    SELECT 
       DATE_FORMAT_TZ(timestamp1, format1, timezone1) as var1,
       DATE_FORMAT_TZ(timestamp1, timezone1) as var2
    FROM T1;
    ```

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|
    |---------------|---------------|
    |1970-01-01 08:00:00|1970-01-01 08:00:00|


