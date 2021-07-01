# REGEXP\_REPLACE

本文为您介绍如何使用REGEXP\_REPLACE函数，用目标字符串替换指定字符串，并返回新的字符串。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR REGEXP_REPLACE(VARCHAR str, VARCHAR pattern, VARCHAR replacement)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|指定的字符串。|
|pattern|VARCHAR|被替换的字符串。|
|replacement|VARCHAR|用于替换的目标字符串。|

**说明：**

-   如果入参为NULL或者正则不合法时，则返回NULL。
-   系统会将pattern和replacement的字符串解析成正则表达式，正则表达式使用[Java Pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html)进行处理，因此pattern和replacement的字符串需遵从Java Regex语法。

## 示例

-   测试数据

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|replace1\(VARCHAR\)|
    |---------------|-------------------|-------------------|
    |2014-03-13|-|‘’|
    |‘‘|-|‘‘|
    |2014-03-13|''|s|
    |2014-03-13|\(|s|
    |100-200|\(\\d+\)|num|

-   测试语句

    ```
    SELECT  REGEXP_REPLACE(str1, pattern1, replace1) as result
    FROM T1;
    ```

-   测试结果

    |result\(VARCHAR\)|
    |-----------------|
    |20140313|
    |空|
    |s2s0s1s4s-s0s3s-s1s3s|
    |null|
    |num-num|


