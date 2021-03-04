# Create a print result table

This topic describes how to create a print result table by using data definition language \(DDL\) statements and configuring parameters in the WITH clause. You can use a print result table to test a streaming job or debug a production job, and check whether the data result meets your expectation.

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
|logger|Specifies whether to display the data result in the console.|No|Valid values:-   false: the default value. The data result is not displayed in the console.
-   true: The data result is displayed in the console. |
|print-identifier|The identifier of the data result.|No|The log information is retrieved by using the identifier of the data result.|
|sink.parallelism|The parallelism of the result table.|No|By default, the value is the same as the parallelism of the upstream storage system.|

## FAQ

-   How can I view print data results in the console?
-   To view print data results, use one of the following two methods:
    -   Go to the development console of fully managed Flink to view data:
        1.  On the **Task Manager** tab of the job that you want to view, click the value in the **Path, ID** column.
        2.  On the **Logs** tab, view the print data result.
    -   Go to Flink UI to view data:
        1.  On the **Overview** tab of the job that you want to view, click **Flink UI**.
        2.          3.  Click **Task Managers**.
        4.  Click the value in the **Path, ID** column.
        5.  On the **Logs** tab, view the print data result.

