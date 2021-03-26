# TO\_TIMESTAMP\_TZ

本文为您介绍如何使用TO\_TIMESTAMP\_TZ函数，将VARCHAR类型的日期根据指定时区转换成TIMESTAMP类型。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
TIMESTAMP TO_TIMESTAMP_TZ(VARCHAR date, VARCHAR timezone)
TIMESTAMP TO_TIMESTAMP_TZ(VARCHAR date, VARCHAR format，VARCHAR timezone)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|timezone|VARCHAR|字符串表示的时区，例如Asia/Shanghai。|
|date|VARCHAR|默认格式为yyyy-MM-dd HH:mm:ss。|
|format|VARCHAR|日期格式。|

## 示例

-   测试数据

    |timestamp1\(VARCHAR\)|format1\(VARCHAR\)|timezone1\(VARCHAR\)|
    |---------------------|------------------|--------------------|
    |1970-01-01 00:00:00|yyyy-MM-dd HH:mm:ss|Asia/Shanghai|

-   测试语句

    ```
    SELECT  
         UNIX_TIMESTAMP(TO_TIMESTAMP_TZ(timestamp1, timezone1)) as var1,
         UNIX_TIMESTAMP(TO_TIMESTAMP_TZ(timestamp1, format1, timezone1)) as var2   
    FROM T1;
    ```

-   测试结果

    |var1\(BIGINT\)|var2\(BIGINT\)|
    |--------------|--------------|
    |-28800|-28800|


