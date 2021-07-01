# Create a print result table

This topic describes how to create a print result table. It also describes data definition language \(DDL\) statements and parameters in the WITH clause. You can use a print result table to test a streaming job or debug a production job, and check whether the data result meets your expectation.

**Note:**

-   If you need to view the result data of the print result table, you must set the level of logs to info because the print sink generates info logs.
-   For security reasons, the system limits the number of data that can be displayed in the taskmanager.log. If you want to check for dirty data or specific data, we recommend that you specify conditions in the WHERE clause to perform the print operation. The print operation ensures that the required data can be checked even if the number of data records that can be displayed is limited.

## DDL syntax

```
CREATE TABLE print_table (
  a INT,
  b varchar
) WITH (
  'connector'='print',
  'logger'='true'
);
```

## Parameters in the WITH clause

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|connector|The type of the result table.|Yes|Set the value to print.|
|logger|Specifies whether to display the data result in the console.|No|Values:-   false: the default value. The data result is not displayed in the console.
-   true: The data result is displayed in the console. |
|print-identifier|The identifier of the data result.|No|The log information is retrieved by using the identifier of the data result.|
|sink.parallelism|The parallelism of the result table.|No|By default, the value is the same as the parallelism of the upstream storage system.|

## FAQ

-   How do I view print data results in the console?
-   To view print data results, you can use one of the following methods:
    -   Log on to the console of fully managed Flink:
        1.  On the **Task Manager** tab of the job that you want to view, click the value in the **Path, ID** column.
        2.  On the **Logs** tab, view the print data result.
    -   Go to Flink UI:
        1.  On the **Overview** tab of the job that you want to view, click **Flink UI**.
        2.  Click **Task Managers**.
        3.  Click the value in the **Path, ID** column.
        4.  On the **Logs** tab, view the data result.

