# JSON\_VALUE

本文为您介绍如何使用JSON\_VALUE函数，从JSON字符串中提取指定路径的表达式。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
VARCHAR JSON_VALUE(VARCHAR content, VARCHAR path)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|content|VARCHAR|需要解析的JSON对象。|
|path|VARCHAR|指定路径表达式。自定义路径需要使用单引号（'），示例如下。 ```
JSON_VALUE(json,'$.passenger_name') AS ABC
```

目前路径支持的符号及功能如下：-   $：根对象。
-   \[\]：数组下标。
-   \*：数组通配符。
-   .：取子元素。 |

**说明：** 如果入参中有不合法的JSON和NULL，则返回为NULL。

## 示例

-   测试数据

    |id\(INT\)|json\(VARCHAR\)|path1\(VARCHAR\)|
    |---------|---------------|----------------|
    |1|\[10, 20, \[30, 40\]\]|$\[2\]\[\*\]|
    |2|\{"aaa":"bbb","ccc":\{"ddd":"eee","fff":"ggg","hhh":\["h0","h1","h2"\]\},"iii":"jjj"\}|$.ccc.hhh\[\*\]|
    |3|\{"aaa":"bbb","ccc":\{"ddd":"eee","fff":"ggg",hhh":\["h0","h1","h2"\]\},"iii":"jjj"\}|$.ccc.hhh\[1\]|
    |4|\[10, 20, \[30, 40\]\]|NULL|
    |5|NULL|$\[2\]\[\*\]|
    |6|"\{xx\]"|"$\[2\]\[\*\]"|

-   测试语句

    ```
    SELECT 
        id，
        JSON_VALUE(json, path1) AS `value`
    FROM 
        T1;
    ```

-   测试结果

    |id \(INT\)|value \(VARCHAR\)|
    |----------|-----------------|
    |1|\[30,40\]|
    |2|\["h0","h1","h2"\]|
    |3|H1|
    |4|NULL|
    |5|NULL|
    |6|NULL|


