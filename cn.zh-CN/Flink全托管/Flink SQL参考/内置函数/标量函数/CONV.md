# CONV

本文为您介绍如何使用CONV函数，对数字或字符类型的数值进行进制转换。

**说明：**

-   本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。
-   CONV\(\)精度为64位。

## 语法

```
VARCHAR CONV(BIGINT number, INT FROM_BASE, INT TO_BASE)
or 
VARCHAR CONV(VARCHAR number, INT FROM_BASE, INT TO_BASE)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|number|BIGINT、VARCHAR|**说明：** 当number为NULL或非法字符时，结果返回为NULL。 |
|FROM\_BASE|INT|取值范围\[2, 36\]。|
|TO\_BASE|INT|可以为正数，取值范围\[2, 36\]；也可以为负数，取值范围\[-36, -2\]。|

## 示例

-   测试数据

    |id\(INT\)|x\(BIGINT\)|y \(VARCHAR\)|
    |---------|-----------|-------------|
    |1|12L|'12'|
    |2|10L|'10'|
    |3|0L|'test'|
    |4|NULL|NULL|

-   测试语句

    ```
    SELECT  id, conv(x, 10, 16) as var1, conv(y, 10, 2) as var2
    FROM T1;
    ```

-   测试结果

    |id\(INT\)|var1\(VARCHAR\)|var2\(VARCHAR\)|
    |---------|---------------|---------------|
    |1|C|1100|
    |2|A|1010|
    |3|0|NULL|
    |4|NULL|NULL|


