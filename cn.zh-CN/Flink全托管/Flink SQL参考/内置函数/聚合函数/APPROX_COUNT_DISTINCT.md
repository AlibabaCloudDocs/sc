# APPROX\_COUNT\_DISTINCT

本文为您可以介绍如何使用APPROX\_COUNT\_DISTINCT函数，提升作业性能。

**说明：** 本文仅适用于VVP 2.4.0且VVR 3.0.0及以上版本。

## 背景信息

Count Distinct优化时，Aggregate节点的State需要保存所有Distinct Key信息。当Distinct Key数目过大时，State的读写开销太大，因此Count Distinct优化存在性能瓶颈。但在很多场景，完全精确的统计并不那么必要。如果您希望牺牲部分精确度来换取性能上的提升，您可以使用新增的内置函数APPROX\_COUNT\_DISTINCT提升作业性能。APPROX\_COUNT\_DISTINCT支持MiniBatch或Local-Global等Aggregate上的优化，但是需要注意以下几点：

-   输入不含有撤回消息。
-   Distinct Key数目需要足够大，例如UV。如果Distinct Key数目不大，APPROX\_COUNT\_DISTINCT性能相对精确计算提升不大。

## 语法

```
APPROX_COUNT_DISTINCT(col [, accuracy])
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|col|任意类型|字段名称|
|accuracy|FLOAT|可选，准确率，取值范围为\(0.0, 1.0\)，默认值为0.99，取值越高，准确率越高，state开销越大，性能越低。|

## 示例

-   测试数据

    |a \(VARCHAR\)|c \(BIGINT\)|
    |-------------|------------|
    |Hi|1|
    |Hi|2|
    |Hi|3|
    |Hi|4|
    |Hi|5|
    |Hi|6|

-   测试代码

    ```
    SELECT 
      a,
      APPROX_COUNT_DISTINCT(b) as b,
      APPROX_COUNT_DISTINCT(b, 0.9) as c
    FROM T1
    GROUP BY a;
    ```

-   测试结果

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |Hi|6|6|


