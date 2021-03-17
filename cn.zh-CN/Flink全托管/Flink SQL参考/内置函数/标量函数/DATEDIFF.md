# DATEDIFF

本文为您介绍如何使用DATEDIFF函数，计算两个日期之间的天数差值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
INT DATEDIFF(VARCHAR enddate, VARCHAR startdate)
INT DATEDIFF(TIMESTAMP enddate, VARCHAR startdate)
INT DATEDIFF(VARCHAR enddate, TIMESTAMP startdate)
INT DATEDIFF(TIMESTAMP enddate, TIMESTAMP startdate)
```

## 入参

|参数|数据类型|
|--|----|
|startdate|TIMESTAMP、VARCHAR|
|enddate|TIMESTAMP、VARCHAR|

**说明：**

-   VARCHAR日期格式：yyyy-MM-dd或yyyy-MM-dd HH:mm:ss。
-   如果入参中任何一个为NULL或解析错误，则返回为NULL。

## 示例

-   测试数据

    |datetime1\(VARCHAR\)|datetime2\(VARCHAR\)|nullstr\(VARCHAR\)|
    |--------------------|--------------------|------------------|
    |2017-10-15 00:00:00|2017-09-15 00:00:00|NULL|

-   测试语句

    ```
    SELECT  DATEDIFF(datetime1, datetime2) as int1, 
            DATEDIFF(TIMESTAMP '2017-10-15 23:00:00',datetime2) as int2, 
            DATEDIFF(datetime2,TIMESTAMP '2017-10-15 23:00:00') as int3, 
            DATEDIFF(datetime2,nullstr) as int4, 
            DATEDIFF(nullstr,TIMESTAMP '2017-10-15 23:00:00') as int5, 
            DATEDIFF(nullstr,datetime2) as int6, 
            DATEDIFF(TIMESTAMP '2017-10-15 23:00:00',TIMESTAMP '2017-9-15 00:00:00')as int7
    FROM T1;
    ```

-   测试结果

    |int1\(INT\)|int2\(INT\)|int3\(INT\)|int4\(INT\)|int5\(INT\)|int6\(INT\)|int7\(INT\)|
    |-----------|-----------|-----------|-----------|-----------|-----------|-----------|
    |30|31|-31|NULL|NULL|NULL|31|


