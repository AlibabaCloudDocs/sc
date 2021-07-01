# APPROX\_COUNT\_DISTINCT

This topic describes how to use the APPROX\_COUNT\_DISTINCT function to improve the performance of your jobs.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Background information

When you optimize the COUNT DISTINCT function, distinct key information must be saved in the state data of the aggregate node. If a large number of distinct keys exist, the read/write overhead of state data is high. This situation causes a bottleneck in job performance optimization. In many cases, accurate computation is not necessary. If you are willing to achieve high job performance at the expense of a small portion of accuracy, you can use the APPROX\_COUNT\_DISTINCT function. APPROX\_COUNT\_DISTINCT supports miniBatch and local-global optimization on the aggregate node. When you use this function, make sure that the following requirements are met:

-   The input data does not contain retract messages.
-   A large number of distinct keys exist, such as unique visits \(UVs\). The APPROX\_COUNT\_DISTINCT function cannot bring obvious benefits if only a small number of distinct keys exist.

## Syntax

```
APPROX_COUNT_DISTINCT(col [, accuracy])
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|col|All data types|The name of the field.|
|accuracy|FLOAT|The computation accuracy. This parameter is optional. A larger value indicates a higher accuracy. A higher state overhead weakens the performance of the APPROX\_COUNT\_DISTINC function. Valid values: \(0.0, 1.0\). Default value: 0.99.|

## Example

-   Test data

    |a \(VARCHAR\)|c \(BIGINT\)|
    |-------------|------------|
    |Hi|1|
    |Hi|2|
    |Hi|3|
    |Hi|4|
    |Hi|5|
    |Hi|6|

-   Test statements

    ```
    SELECT 
      a,
      APPROX_COUNT_DISTINCT(b) as b,
      APPROX_COUNT_DISTINCT(b, 0.9) as c
    FROM T1
    GROUP BY a;
    ```

-   Test result

    |a \(VARCHAR\)|b \(BIGINT\)|c \(BIGINT\)|
    |-------------|------------|------------|
    |Hi|6|6|


