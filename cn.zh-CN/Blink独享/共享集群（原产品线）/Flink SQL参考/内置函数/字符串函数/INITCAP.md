# INITCAP

本文为您介绍如何使用实时计算字符串函数INITCAP。

## 语法

```
 VARCHAR INITCAP(A)
```

## 入参

|参数|数据类型|
|--|----|
|A|VARCHAR|

## 功能描述

返回字符串，每个字转换器的第一个字母大写，其余为小写。

## 示例

-   测试数据

    |var1\(VARCHAR\)|
    |---------------|
    |aADvbn|

-   测试语句

    ```
    SELECT INITCAP(var1)as aa
    FROM T1;  
    ```

-   测试结果

    |aa\(VARCHAR\)|
    |-------------|
    |Aadvbn|


