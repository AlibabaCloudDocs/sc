# DATE\_FORMAT

本文为您介绍如何使用DATE\_FORMAT函数，将字符串类型的日期从原格式转换至目标格式。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR DATE_FORMAT(TIMESTAMP time, VARCHAR to_format)
VARCHAR DATE_FORMAT(VARCHAR date, VARCHAR to_format)
VARCHAR DATE_FORMAT(VARCHAR date, VARCHAR from_format, VARCHAR to_format)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|time|TIMESTAMP|源字符串。|
|date|VARCHAR|源字符串，默认格式为：yyyy-MM-dd HH:mm:ss。|
|from\_format|VARCHAR|可选，原格式，默认格式为yyyy-MM-dd hh:mm:ss。|
|to\_format|VARCHAR|目标格式。|

**说明：** 如果入参中任何一个为NULL或解析错误，则返回为NULL。

## 示例

-   测试数据

    |date1\(VARCHAR\)|datetime1\(VARCHAR\)|nullstr\(VARCHAR\)|
    |----------------|--------------------|------------------|
    |0915-2017|2017-09-15 00:00:00|NULL|

-   测试语句

    ```
    SELECT DATE_FORMAT(datetime1, 'yyMMdd') as var1,
     DATE_FORMAT(nullstr, 'yyMMdd') as var2,
     DATE_FORMAT(datetime1, nullstr) as var3,
     DATE_FORMAT(date1, 'MMdd-yyyy', nullstr) as var4,
     DATE_FORMAT(date1, 'MMdd-yyyy', 'yyyyMMdd') as var5,
     DATE_FORMAT(TIMESTAMP '2017-09-15 23:00:00', 'yyMMdd') as var6
    FROM T1;
    ```

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|var4\(VARCHAR\)|var5\(VARCHAR\)|var6\(VARCHAR\)|
    |---------------|---------------|---------------|---------------|---------------|---------------|
    |170915|null|null|null|20170915|170915|


