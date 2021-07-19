---
keyword: [INSERT INTO, Sink]
---

# INSERT INTO statement

This topic describes how to write data to one or more sinks in a job.

## Example of how to write data to a sink

-   Source table

    ```
    CREATE TABLE datagen_source (
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'datagen' 
    );
    ```

-   Result table

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


## Example of how to write data to multiple sinks

-   Source table

    ```
    CREATE TABLE datagen_source (
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'datagen'  
    );
    ```

-   Result table A

    ```
    CREATE TABLE blackhole_sinkA(
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'blackhole' 
    );
    ```

-   Result table B

    ```
    CREATE TABLE blackhole_sinkB(
      name VARCHAR,
      score BIGINT
    ) WITH (
      'connector' = 'blackhole' 
    );
    ```

-   DML

    In this example, data is written to two sinks.

    ```
    BEGIN STATEMENT SET;      -- This is required when you write data to multiple sinks. 
    INSERT INTO blackhole_sinkA 
      SELECT UPPER(name), sum(score) 
      FROM datagen_source 
      GROUP BY UPPER(name);
    INSERT INTO blackhole_sinkB 
      SELECT LOWER(name), max(score) 
      FROM datagen_source 
      GROUP BY LOWER(name);
    END;      -- This is required when you write data to multiple sinks. 
    ```

    **Note:** The statement that writes data to multiple sinks must start with BEGIN STATEMENT SET; and end with END;.


