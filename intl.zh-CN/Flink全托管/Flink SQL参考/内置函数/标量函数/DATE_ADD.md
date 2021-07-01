# DATE\_ADD

本文为您介绍如何使用DATE\_ADD函数，返回指定日期增加目标天数后的日期。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR DATE_ADD(VARCHAR startdate, INT days)
VARCHAR DATE_ADD(TIMESTAMP time, INT days)
```

## 入参

|参数|数据类型|
|--|----|
|startdate|TIMESTAMP、VARCHAR **说明：** VARCHAR类型日期格式为yyyy-MM-dd或yyyy-MM-dd HH:mm:ss。 |
|time|TIMESTAMP|
|days|INT|

**说明：** 如果入参中任何一个为NULL或解析错误，则返回为NULL。

## 示例

-   测试数据

    |datetime1\(VATCHAR\)|nullstr\(VATCHAR\)|
    |--------------------|------------------|
    |2017-09-15 00:00:00|null|

-   测试语句

    ```
    SELECT DATE_ADD(datetime1, 30) as var1,
     DATE_ADD(TIMESTAMP '2017-09-15 23:00:00',30) as var2,
     DATE_ADD(nullstr,30) as var3
    FROM T1;   
    ```

-   测试结果

    |var1\(VARCHAR\)|var2\(VARCHAR\)|var3\(VARCHAR\)|
    |---------------|---------------|---------------|
    |2017-10-15|2017-10-15|null|


