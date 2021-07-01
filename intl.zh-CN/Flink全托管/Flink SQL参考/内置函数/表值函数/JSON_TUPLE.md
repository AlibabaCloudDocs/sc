# JSON\_TUPLE

本文为您介绍如何使用JSON\_TUPLE函数，从JSON字符串中取出各路径字符串所表示的值。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 语法

```
JSON_TUPLE(str, path1, path2 ..., pathN)     
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|str|VARCHAR|JSON字符串。|
|path1～pathN|VARCHAR|表示路径的字符串，前面不需要`$`。|

## 示例

-   测试数据

    |d\(VARCHAR\)|s\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|qwe3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|qwe2|

-   测试语句

    ```
    SELECT d, v 
    FROM T1, lateral table(JSON_TUPLE(d, 'qwe', s))
    AS T(v);   
    ```

-   测试结果

    |d\(VARCHAR\)|v\(VARCHAR\)|
    |------------|------------|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd|
    |\{"qwe":"asd","qwe2":"asd2","qwe3":"asd3"\}|asd3|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd4|
    |\{"qwe":"asd4","qwe2":"asd5","qwe3":"asd3"\}|asd5|


