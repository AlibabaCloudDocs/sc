---
keyword: data views
---

# Create a data view

This topic describes how to create a data view in Realtime Compute for Apache Flink to simplify the development process.

## Background information

If your business logic is complex and you need to write nested statements in DML statements, it can be difficult to locate problems. To simplify the development process, you can define a data view and write nested statements to the data view.

**Note:** A data view displays a logical table that describes the computing logic without storing physical data.

## Syntax

```
CREATE TEMPORARY VIEW viewName AS  [ (columnName[ , columnName]* ) ] queryStatement;
```

-   viewName: the name of the view.
-   columnName: the name of the field.
-   queryStatement: the alias of the nested statement.

## Example

```
CREATE TEMPORARY VIEW tmp_view AS 
SELECT 
    * 
FROM 
    datagen_source;

INSERT INTO
    rds_output
SELECT
    r,
    t,
    c,
    u
FROM
    tmp_view;
```

