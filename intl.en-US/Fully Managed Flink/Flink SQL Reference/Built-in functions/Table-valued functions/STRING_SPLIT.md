# STRING\_SPLIT

This topic describes how to use the STRING\_SPLIT function. This function splits a string into substrings based on the specified delimiter and returns a list of substrings.

**Note:** This function is supported only in VVP 2.4.0 and VVR 3.0.0 and later.

## Syntax

```
string_split(string, separator)
```

## Input parameters

|Parameter|Data type|Description|
|---------|---------|-----------|
|string|VARCHAR|The string that you want to split.|
|separator|VARCHAR|The delimiter. **Note:** The delimiter must be a single string. |

**Note:**

-   If the value of a string is NULL, an empty row is returned.
-   If the string contains two or more consecutive delimiters, an empty substring that does not contain characters is returned.
-   If the string does not contain the specified delimiter, the whole string that you want to split is returned.

## Example

-   Test data

    |d\(varchar\)|s\(varchar\)|
    |------------|------------|
    |abc-bcd|-|
    |hhh|-|

-   Test statements

    ```
    SELECT d,v 
    FROM T1, 
    lateral table(string_split(d, s)) as T(v);
    ```

-   Test result

    |d\(varchar\)|v\(varchar\)|
    |------------|------------|
    |abc-bcd|abc|
    |abc-bcd|bcd|
    |hhh|hhh|


