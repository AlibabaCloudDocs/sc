# MURMUR\_HASH

The topic describes how to use the MURMUR\_HASH function. This function specifies the 32-bit hash value for a parameter by using the hash32x86 method.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
INT MURMUR_HASH(value)
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
    SELECT  MURMUR_HASH(value) as intt
    FROM T1;         
    ```

-   Test result

    |intt\(VARCHAR\)|
    |---------------|
    |740959099|
    |NULL|
    |271330425|


## Example 2

-   Test data

    |str|
    |---|
    |4.5|
    |NULL|
    |4|

-   Test statements

    ```
    SELECT MURMUR_HASH(CAST(str AS FLOAT)) as intt
    FROM T2;         
    ```

-   Test result

    |intt\(VARCHAR\)|
    |---------------|
    |-2130121231|
    |NULL|
    |1863595885|


