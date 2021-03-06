# IS NOT TRUE {#concept_62814_zh .concept}

This topic describes how to use the logical operation function IS NOT TRUE of Realtime Compute.

## Syntax {#section_mnm_4dw_cgb .section}

```language-sql
A IS NOT TRUE

```

## Input parameter {#section_qvx_tdw_cgb .section}

|Name|Data type|
|----|---------|
|A|BOOLEAN|

## Function description {#section_cl2_v2w_cgb .section}

If A is TRUE, FALSE is returned. If A is FALSE, TRUE is returned.

## Example {#section_ekm_zdw_cgb .section}

-   Test data

    |int1 \(INT\)|int2 \(INT\)|
    |------------|------------|
    |255|97|

-   Test statement

    ```
    SELECT int1 as aa
    FROM T1
    WHERE int1=25 IS NOT TRUE;
    
    ```

-   Test result

    |aa \(INT\)|
    |----------|
    |97|


