# MURMUR\_HASH\_64

The topic describes how to use the MURMUR\_HASH\_64 function. This function specifies the 64-bit hash value for a parameter by using the hash128x64 method.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
BIGINT MURMUR_HASH_64(value)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|value|VARCHAR or a numeric value|

## Example 1

-   Test data

    |value|
    |-----|
    |This is a test String.|
    |NULL|
    |test|

-   Test statements

    ```
    SELECT  MURMUR_HASH_64(value) as big
    FROM T1;         
    ```

-   Test result

    |big\(VARCHAR\)|
    |--------------|
    |2706826604463634835|
    |NULL|
    |5010266691562018262|


## Example 2

-   Test data

    |value|
    |-----|
    |4.5|
    |NULL|
    |4|

-   Test statements

    ```
    SELECT MURMUR_HASH_64(CAST(value AS FLOAT)) as big
    FROM T2;         
    ```

-   Test result

    |big\(VARCHAR\)|
    |--------------|
    |8033179276572534397|
    |NULL|
    |-6697622088025985219|


