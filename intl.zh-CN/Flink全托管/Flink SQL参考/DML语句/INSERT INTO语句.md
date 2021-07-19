---
keyword: [INSERT INTO, Sink]
---

# INSERT INTO语句

本文为您介绍如何在一个作业中写入一个Sink或多个Sink。

## 写入一个Sink示例

-   源表

    ```
    CREATE TABLE datagen_source (
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'datagen' 
    );
    ```

-   结果表

    ```
    create table blackhole_sink(
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'blackhole' 
    );
    ```

-   DML

    ```
    INSERT INTO blackhole_sink SELECT UPPER(name), score FROM datagen_source;
    ```


## 写入多个Sink示例

-   源表

    ```
    CREATE TABLE datagen_source (
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'datagen'  
    );
    ```

-   结果表A

    ```
    CREATE TABLE blackhole_sinkA(
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'blackhole' 
    );
    ```

-   结果表B

    ```
    CREATE TABLE blackhole_sinkB(
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'blackhole' 
    );
    ```

-   DML

    本文以写入两个Sink为例进行介绍。

    ```
    BEGIN STATEMENT SET;      --写入多个Sink时，必填。
    INSERT INTO blackhole_sinkA 
      SELECT UPPER(name), sum(score) 
      FROM datagen_source 
      GROUP BY UPPER(name);
    INSERT INTO blackhole_sinkB 
      SELECT LOWER(name), max(score) 
      FROM datagen_source 
      GROUP BY LOWER(name);
    END;      --写入多个Sink时，必填。
    ```

    **说明：** 写入多个Sink语句时，需要以BEGIN STATEMENT SET;开头，以END;结尾。


