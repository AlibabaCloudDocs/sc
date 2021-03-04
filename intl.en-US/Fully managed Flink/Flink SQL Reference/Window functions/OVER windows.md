# OVER windows

An OVER window is a standard window used in traditional databases. Over aggregates are different from window aggregates that are based on GROUP BY clauses. If streaming data uses OVER windows, each element corresponds to an OVER window. An OVER window can be determined based on an actual row or an actual value \(timestamp value\) of an element. Elements of a stream may be distributed across multiple windows.

In a stream that uses OVER windows, each element corresponds to an OVER window and triggers data computing once. The row determined by each element that triggers computing is the last row of the window where the element is located. In the underlying implementation of Realtime Compute for Apache Flink, data for OVER windows is centrally managed. Only one copy of the data is stored. Logically, an OVER window is created for each element. Realtime Compute for Apache Flink calculates the data for each OVER window and then deletes the data that is no longer used after the calculation is complete.

## Syntax

```
SELECT
    agg1(col1) OVER (definition1) AS colName,
    ...
    aggN(colN) OVER (definition1) AS colNameN
FROM Tab1;
```

-   agg1\(col1\): aggregates input data based on the col1 column specified by GROUP BY.
-   OVER \(definition1\): defines an OVER window.
-   AS colName: specifies the alias of a column.

**Note:**

-   OVER \(definition1\) for agg1 through aggN must be the same.
-   The alias specified by AS can be queried by using an outer SQL statement.

## Window types

In Flink SQL, OVER windows are defined in compliance with standard SQL syntax. Traditional OVER windows are not classified into fine-grained window types. OVER windows are classified into the following two types based on how computed rows are determined:

-   ROWS OVER windows: Each row of elements is treated as a new computed row. A new window is generated for each row.
-   RANGE OVER windows: All rows of elements with the same timestamp are treated as one computed row and are assigned to the same window.

## Attributes

|Orthogonal attribute|Description|proctime|eventtime|
|--------------------|-----------|--------|---------|
|ROWS OVER Window|A window is determined based on the actual row of an element.|Supported|Supported|
|RANGE OVER Window|A window is determined based on the timestamp value of an element.|Supported|Supported|

## ROWS OVER windows

-   Description

    For a ROWS OVER window, each element is assigned to a window. ROWS OVER windows come in two types: unbounded and bounded.

    The following figure shows unbounded ROWS OVER windows.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1948209951/p34339.png)

    **Note:** The preceding figure shows that elements in windows w7 and w8 of user 1 arrive at the same time, and elements in windows w3 and w4 of user 2 arrive at the same time. However, the elements are assigned to different windows. ROWS OVER windows are different from RANGE OVER windows in this aspect.

    The following figure shows bounded ROWS OVER windows. Each window has three elements: the current element and the two elements that precede the current element.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1948209951/p34340.png)

    **Note:** The preceding figure shows that elements in windows w5 and w6 of user 1 arrive at the same time, and elements in windows w1 and w2 of user 2 arrive at the same time. However, the elements are assigned to different windows. ROWS OVER windows are different from RANGE OVER windows in this aspect.

-   Syntax

    ```
    SELECT
        agg1(col1) OVER(
         [PARTITION BY (value_expression1,..., value_expressionN)]
         ORDER BY timeCol
         ROWS 
         BETWEEN (UNBOUNDED | rowCount) PRECEDING AND CURRENT ROW) AS colName, ...
    FROM Tab1;       
    ```

    -   value\_expression: specifies the value expression used for partitioning.
    -   timeCol: specifies the time field used to sort elements.
    -   rowCount: specifies the number of rows that precede the current row.
-   Example

    This example describes bounded ROWS OVER windows. Assume that an on-sale product table contains item IDs, item types, on-sale time, and prices. Calculate the highest price among the three products similar to the current product before the current product is on sale.

    -   Test data

        |Item ID|Item type|On-sale time|Price|
        |-------|---------|------------|-----|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|

    -   Test statements

        ```
        CREATE TEMPORARY TABLE tmall_item(
          itemID VARCHAR,
          itemType VARCHAR,
          eventtime varchar,                            
          onSellTime AS TO_TIMESTAMP(eventtime),
          price DOUBLE,
          WATERMARK FOR onSellTime AS onSellTime - INTERVAL '0' SECOND  -- Define a watermark for the rowtime.
        ) WITH (
          'connector' = 'sls',
           ...
        );
        
        SELECT
            itemID,
            itemType,
            onSellTime,
            price,  
            MAX(price) OVER (
                PARTITION BY itemType 
                ORDER BY onSellTime 
                ROWS BETWEEN 2 preceding AND CURRENT ROW) AS maxPrice
        FROM tmall_item;
        ```

    -   Test results

        |itemID|itemType|onSellTime|price|maxPrice|
        |------|--------|----------|-----|--------|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|50|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|60|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|60|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|20|


## RANGE OVER windows

-   Description

    For a RANGE OVER window, all elements with the same timestamp are assigned to the same window. RANGE OVER windows come in two types: unbounded and bounded.

    The following figure shows unbounded RANGE OVER windows.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1948209951/p34341.png)

    **Note:** The preceding figure shows that elements in the two w7 windows of user 1 arrive at the same time, and elements in the two w3 windows of user 2 arrive at the same time. Elements with the same timestamp are assigned to the same window. RANGE OVER windows are different from ROWS OVER windows in this aspect.

    The following figure shows bounded RANGE OVER windows, in which a 3-second window has an interval of 2 seconds: `(INTERVAL '2' SECOND)`.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1948209951/p34342.png)

    **Note:** The preceding figure shows that elements in the two w6 windows of user 1 arrive at the same time, and elements in the two w3 windows of user 2 arrive at the same time. Elements with the same timestamp are assigned to the same window. RANGE OVER windows are different from ROWS OVER windows in this aspect.

-   Syntax

    ```
    SELECT
        agg1(col1) OVER(
         [PARTITION BY (value_expression1,..., value_expressionN)]
         ORDER BY timeCol
         RANGE 
         BETWEEN (UNBOUNDED | timeInterval) PRECEDING AND CURRENT ROW) AS colName,
    ...
    FROM Tab1;
    ```

    -   value\_expression: specifies the value expression used for partitioning.
    -   timeCol: specifies the time field used to sort elements.
    -   timeInterval: specifies the time interval between the time of the current row and that of the element row to which it can be traced back.
-   Example

    This example describes bounded RANGE OVER windows. Assume that an on-sale product table contains item IDs, item types, on-sale time, and prices. Calculate the highest price among similar products that are on sale 2 minutes earlier than the current product.

    -   Test data

        |Item ID|Item type|On-sale time|Price|
        |-------|---------|------------|-----|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|

    -   Test statements

        ```
        CREATE TEMPORARY TABLE tmall_item(
          itemID VARCHAR,
          itemType VARCHAR,
          eventtime varchar,                            
          onSellTime AS TO_TIMESTAMP(eventtime),
          price DOUBLE,
          WATERMARK FOR onSellTime AS onSellTime - INTERVAL '0' SECOND  -- Define a watermark for the rowtime.
        ) WITH (
          'connector' = 'sls',
           ...
        );
        
        
        SELECT  
            itemID,
            itemType, 
            onSellTime, 
            price,  
            MAX(price) OVER (
                PARTITION BY itemType 
                ORDER BY onSellTime 
                RANGE BETWEEN INTERVAL '2' MINUTE preceding AND CURRENT ROW) AS maxPrice
        FROM tmall_item;        
        ```

    -   Test results

        |itemID|itemType|onSellTime|price|maxPrice|
        |------|--------|----------|-----|--------|
        |ITEM001|Electronic|`2017-11-11 10:01:00`|20|20|
        |ITEM002|Electronic|`2017-11-11 10:02:00`|50|50|
        |ITEM003|Electronic|`2017-11-11 10:03:00`|30|50|
        |ITEM004|Electronic|`2017-11-11 10:03:00`|60|60|
        |ITEM005|Electronic|`2017-11-11 10:05:00`|40|60|
        |ITEM006|Electronic|`2017-11-11 10:06:00`|20|40|
        |ITEM007|Electronic|`2017-11-11 10:07:00`|70|70|
        |ITEM008|Clothes|`2017-11-11 10:08:00`|20|20|


