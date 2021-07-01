# UNIX\_TIMESTAMP

本文为您介绍如何使用UNIX\_TIMESTAMP函数，将入参转换为长整型的时间戳。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
BIGINT UNIX_TIMESTAMP(VARCHAR date)
BIGINT UNIX_TIMESTAMP(TIMESTAMP timestamp)
BIGINT UNIX_TIMESTAMP(VARCHAR date, VARCHAR format)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|timestamp|TIMESTAMP|无。|
|date|VARCHAR|默认日期格式为`yyyy-MM-dd HH:mm:ss`。|
|format|VARCHAR|默认日期格式为`yyyy-MM-dd hh:mm:ss`。|

## 示例

-   测试数据

    |date1\(VARCHAR\)|date2\(VARCHAR\)|
    |----------------|----------------|
    |2021-03-25 00:00:00|1970-01-01 00:00:00|

-   测试语句

    ```
    SELECT UNIX_TIMESTAMP(to_timestamp_tz(date1)) as big1,
           UNIX_TIMESTAMP(date2) as big2
    FROM T1;
    ```

-   测试结果

    |big1（BIGINT）|big2（BIGINT）|
    |------------|------------|
    |1616601600|-28800|


