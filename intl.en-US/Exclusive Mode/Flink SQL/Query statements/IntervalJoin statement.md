---
keyword: [IntervalJoin, time-windowed JOIN]
---

# IntervalJoin statement

The IntervalJoin statement allows two streams to be joined. During the JOIN operation, each record in the left and right streams is associated with only data generated at the same time in the other stream. After the streams are joined, the time column in the input stream is still retained for you to continue to perform operations based on the event time.

## Syntax

```
SELECT column-names
FROM table1  [AS <alias1>]
[INNER | LEFT | RIGHT |FULL ] JOIN table2 
ON table1.column-name1 = table2.key-name1 AND TIMEBOUND_EXPRESSION
```

**Note:**

-   INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL JOIN are supported. If you use JOIN directly, INNER JOIN is automatically used.
-   SEMI JOIN or ANTI JOIN is not supported.
-   TIMEBOUND\_EXPRESSION is an interval conditional expression on the columns of the time attributes of the left and right streams. The following conditional expressions are supported:
    -   ltime = rtime
    -   ltime \>= rtime AND ltime < rtime + INTERVAL '10' MINUTE
    -   ltime BETWEEN rtime - INTERVAL '10' SECOND AND rtime + INTERVAL '5' SECOND

## Example 1 \(Event time-based\)

This example shows the statistics on the logistics information within 4 hours after orders are placed.

-   Test data
    -   Order table \(orders\)

        |id|productName|orderTime|
        |--|-----------|---------|
        |1|iphone|2020-04-01 10:00:00.0|
        |2|mac|2020-04-01 10:02:00.0|
        |3|huawei|2020-04-01 10:03:00.0|
        |4|pad|2020-04-01 10:05:00.0|

    -   Logistics table \(shipments\)

        |shipId|orderId|status|shiptime|
        |------|-------|------|--------|
        |0|1|shipped|2020-04-01 11:00:00.0|
        |1|2|delivered|2020-04-01 17:00:00.0|
        |2|3|shipped|2020-04-01 12:00:00.0|
        |3|4|shipped|2020-04-01 11:30:00.0|

-   Test statements

    ```
    CREATE TABLE Orders(
      id BIGINT,
      productName VARCHAR,
      orderTime TIMESTAMP,
      WATERMARK wk FOR orderTime as withOffset(orderTime, 2000)  --Define a watermark for the rowtime.
    ) WITH ( 
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    CREATE TABLE Shipments(
      shipId BIGINT,
      orderId BIGINT,
      status VARCHAR,
      shiptime TIMESTAMP,
      WATERMARK wk FOR ts as withOffset(shiptime, 2000) --Define a watermark for the rowtime. 
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    --Create an ApsaraDB RDS result table.
    CREATE TABLE rds_output(
      id BIGINT,
      productName VARCHAR,
      status VARCHAR
    ) WITH (
      type='rds',
      url='<yourDatabaseURL>',
      tableName='<yourDatabaseTablename>',
      userName='<yourDatabaseUserName>',
      password='<yourDatabasePassword>'
    );
    
    INSERT INTO rds_output
    SELECT id, productName, status
    FROM Orders AS o
    JOIN Shipments AS s on o.id = s.orderId AND
         o.ordertime BETWEEN s.shiptime - INTERVAL '4' HOUR AND s.shiptime;
    ```

-   Test result

    |id\(bigint\)|productName\(varchar\)|status\(varchar\)|
    |------------|----------------------|-----------------|
    |1|iphone|shipped|
    |3|huawei|shipped|
    |4|pad|shipped|


## Example 2 \(Processing time-based\)

-   Test data
    -   datahub\_stream1

        |k1|v1|
        |--|--|
        |1|val1|
        |2|val2|
        |3|val3|

    -   datahub\_stream2

        |k1|v1|
        |--|--|
        |1|val1|
        |2|val2|
        |3|val3|

-   Test statements

    ```
    CREATE TABLE datahub_stream1 (
      k1 BIGINT,
      v1 VARCHAR,
      d AS PROCTIME()
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    CREATE TABLE datahub_stream2 (
      k2 BIGINT,
      v2 VARCHAR,
      e AS PROCTIME()
    ) WITH (
      type='datahub',
      endpoint='<yourEndpoint>',
      accessId='<yourAccessID>',
      accessKey='<yourAccessSecret>',
      projectName='<yourProjectName>',
      topic='<yourTopic>',
      project='<yourProjectName>'
    );
    
    --Create an ApsaraDB RDS result table.
    CREATE TABLE rds_output(
      k1 BIGINT,
      v1 VARCHAR,
      v2 VARCHAR
    ) WITH (
      type='rds',
      url='<yourDatabaseURL>',
      tableName='<yourDatabaseTablename>',
      userName='<yourDatabaseUserName>',
      password='<yourDatabasePassword>'
    );
    
    INSERT INTO rds_output
    SELECT k1, v1, v2
    FROM datahub_stream1 AS o
    JOIN datahub_stream2 AS s on o.k1 = s.k2 AND
         o.d BETWEEN s.e - INTERVAL '4' MINUTE AND s.e;
    ```


**Note:** The results are uncertain because they depend on the time when each data record in the two data streams enters the system. Therefore, this example does not provide the expected results.

