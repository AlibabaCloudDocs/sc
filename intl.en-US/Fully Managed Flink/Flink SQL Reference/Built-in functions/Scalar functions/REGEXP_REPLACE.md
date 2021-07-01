# REGEXP\_REPLACE

This topic describes how to use the REGEXP\_REPLACE function. This function replaces the specified string with the desired string and returns a new string.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
VARCHAR REGEXP_REPLACE(VARCHAR str, VARCHAR pattern, VARCHAR replacement)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|str|VARCHAR|The specified string.|
|pattern|VARCHAR|The original string that you want to replace.|
|replacement|VARCHAR|The string that is used to replace the original string.|

**Note:**

-   If an input parameter is NULL or the regular expression is invalid, NULL is returned.
-   The system parses the pattern and replacement parameters into regular expressions. The regular expressions are processed by using [Java Pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html). Therefore, the pattern and replacement parameters must follow the syntax for Java Regex.

## Example

-   Test data

    |str1\(VARCHAR\)|pattern1\(VARCHAR\)|replace1\(VARCHAR\)|
    |---------------|-------------------|-------------------|
    |2014-03-13|-|"|
    |"|-|"|
    |2014-03-13|''|s|
    |2014-03-13|\(|s|
    |100-200|\(\\d+\)|num|

-   Test statements

    ```
    SELECT  REGEXP_REPLACE(str1, pattern1, replace1) as result
    FROM T1;
    ```

-   Test result

    |result\(VARCHAR\)|
    |-----------------|
    |20140313|
    |Empty string|
    |s2s0s1s4s-s0s3s-s1s3s|
    |null|
    |num-num|


