# STRING\_SPLIT

本文为您介绍如何使用STRING\_SPLIT函数，根据指定的分隔符将目标字符串拆分为子字符串，并返回子字符串列表。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
string_split(string, separator)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|string|VARCHAR|目标字符串。|
|separator|VARCHAR|指定的分隔符。**说明：** separator暂不支持多字符串形式，只支持单字符串形式。 |

**说明：**

-   如果string为NULL，则返回一个空行。
-   如果string包含两个或多个连续出现的分隔符时，则返回长度为零的空子字符串。
-   如果string未包含指定分隔符，则返回目标字符串。

## 示例

-   测试数据

    |d\(varchar\)|s\(varchar\)|
    |------------|------------|
    |abc-bcd|-|
    |hhh|-|

-   测试语句

    ```
    SELECT d,v 
    FROM T1, 
    lateral table(string_split(d, s)) as T(v);
    ```

-   测试结果

    |d\(varchar\)|v\(varchar\)|
    |------------|------------|
    |abc-bcd|abc|
    |abc-bcd|bcd|
    |hhh|hhh|


