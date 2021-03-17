# SPLIT\_INDEX

本文为您介绍如何使用SPLIT\_INDEX函数，将目标字符串按照指定分隔符分割成若干段，返回指定位置的字段信息。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR SPLIT_INDEX(VARCHAR str, VARCHAR sep, INT index)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|目标字符串。|
|sep|VARCHAR|分隔符。|
|index|INT|字段位置，index从0开始。**说明：** 如果获取不到字段，则返回NULL。 |

**说明：** 如果入参任何一个为NULL，则返回NULL。

## 示例

-   测试数据

    |str\(VARCHAR\)|sep\(VARCHAR\)|index\(INT\)|
    |--------------|--------------|------------|
    |Jack,John,Mary|,|2|
    |Jack,John,Mary|,|3|
    |Jack,John,Mary|NULL|0|
    |NULL|,|0|

-   测试语句

    ```
    SELECT  SPLIT_INDEX(str, sep, index) as var1
    FROM T1;         
    ```

-   测试结果

    |var1\(VARCHAR\)|
    |---------------|
    |Mary|
    |NULL|
    |NULL|
    |NULL|


