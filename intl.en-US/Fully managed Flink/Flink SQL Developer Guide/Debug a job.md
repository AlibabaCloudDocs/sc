# Debug a job

This topic describes how to enable job debugging to simulate job running, check outputs, and verify the business logic of SELECT statements. This makes job development efficient and reduces the risks of poor data quality.

A session cluster for SQL previews is created. Before job debugging, the system checks whether a session cluster that allows SQL previews exists. If no such a session cluster exists, the system prompts and guides you to configure a session cluster that allows SQL previews. For more information about how to configure a session cluster, see[Configure a session cluster](/intl.en-US/Fully managed Flink/Configure a session cluster.md).

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  Register a source table and a result table in a separate manner. You can use one of the following methods:

    -   Write a complete statement to create a table.
        1.  In the left-side navigation pane, click **SQL Editor**.
        2.  Click **New Script**.
        3.  Enter a data definition language \(DDL\) statement to define a source table or result table.
        4.  Click **Validate**.
        5.  Click **Execute**.

            In the **Tables** list, the table that is registered appears. You can click the table name to view the table fields and data types.

    -   Modify the definition based on the statement template of the connector.
        1.  In the left-side navigation pane, click **SQL Editor**.
        2.  Click the **Schemas** tab.
        3.  Click the ![Create a table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8297614161/p133649.png) icon next to **Tables**.
        4.  Click the connector that you want to use.

            SQL Editor displays the SQL statement that is used to create a table.

            ![Use an SQL statement to create a table](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7596604161/p133654.png)

        5.  In the script editing box, modify the schema parameters and the parameters in the WITH clause.

            **Note:** Before you use a user-defined function \(UDF\) in an SQL statement, register the UDF. Only the registered UDFs can be used. For information about how to register a UDF, see [Manage UDFs]().

        6.  Click **Validate**.
        7.  Click **Execute**.

            In the **Tables** list of the **Schemas** tab, the table that is registered appears. You can click the table name to view the table fields and data types.

4.  On the **Scripts** tab, click **New Script**.

5.  Write and debug an SQL statement.

    The following example shows the simple logic:

    ```
    SELECT xxx FROM Source table;
    ```

6.  Click **Validate**.

    In the lower part of the SQL script editing page, you can view the verification result of the SQL syntax. Only SQL scripts that have passed the verification can be executed.

7.  Click **Execute**.

    After you click **Execute**, the debugging result appears in the lower part of SQL Editor.

    ![Debugging result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7007604161/p187809.png)

    **Note:** By default, fully managed Flink reads a maximum of 1,000 records of data. When the maximum value is reached, the system stops data read operations.


