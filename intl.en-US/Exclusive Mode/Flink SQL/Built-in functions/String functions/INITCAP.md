# INITCAP

This topic describes how to use the string function INITCAP in Realtime Compute.

## Syntax

```
 VARCHAR INITCAP(A)
```

## Input parameters

|Parameter|Data type|
|---------|---------|
|A|VARCHAR|

## Description

Returns a string in which the first letter of each word is uppercase and all other letters are lowercase.

## Example

-   Test data

    |var1 \(VARCHAR\)|
    |----------------|
    |aADvbn|

-   Test statements

    ```
    SELECT INITCAP(var1)as aa
    FROM T1;  
    ```

-   Test results

    |aa \(VARCHAR\)|
    |--------------|
    |Aasvbn|


