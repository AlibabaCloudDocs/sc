# DATE\_SUB

本文为您介绍如何使用DATE\_SUB函数，返回指定日期减去目标天数后的日期。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR DATE_SUB(VARCHAR startdate, INT days)
VARCHAR DATE_SUB(TIMESTAMP time, INT days)
```

## 入参

|参数|数据类型|
|--|----|
|startdate|VARCHAR **说明：** VARCHAR类型日期格式为yyyy-MM-dd或yyyy-MM-dd HH:mm:ss。 |
|time|TIMESTAMP|
|days|INT|

**说明：** 如果入参中任何一个为NULL或解析错误，则返回为NULL。

## 示例

-   测试数据

    |date1\(VARCHAR\)|nullstr\(VARCHAR\)|
    |----------------|------------------|
    |2017-10-15|NULL|

-   测试语句

    ```
    SELECT DATE_SUB(date1, 30) as var1,
     DATE_SUB(TIMESTAMP '2017-10-15 23:00:00',30) as var2,
     DATE_SUB(nullstr,30) as var3
    FROM T1;
    ```

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-09-15|2017-09-15|NULL|


