# TO\_DATE

本文为您介绍如何使用TO\_DATE函数，将INT类型或者VARCHAR类型的日期转换成DATE类型。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
Date TO_DATE(INT time)
Date TO_DATE(VARCHAR date)
Date TO_DATE(VARCHAR date,VARCHAR format)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|time|INT|表示从1970-1-1到所表示时间之间天数。|
|date|VARCHAR|默认格式为yyyy-MM-dd。|
|format|VARCHAR|日期格式。|

## 示例

-   测试数据

    |date1\(INT\)|date2\(VARCHAR\)|date3\(VARCHAR\)|
    |------------|----------------|----------------|
    |100|2017-09-15|20170915|

-   测试语句

    ```
    SELECT TO_DATE(date1) as var1,
     TO_DATE(date2) as var2,
     TO_DATE(date3,'yyyyMMdd') as var3
    FROM T1;
    ```

-   测试结果

    |var1\(DATE\)|var2\(DATE\)|var3\(DATE\)|
    |------------|------------|------------|
    |1970-04-11|2017-09-15|2017-09-15|


