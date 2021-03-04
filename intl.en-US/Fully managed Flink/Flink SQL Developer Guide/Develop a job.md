---
keyword: [fully managed Flink, development, SQL jobs]
---

# Develop a job

This topic describes the limits and methods to develop an SQL job in fully managed Flink.

## Limits

-   To use SQL Editor to submit an SQL job, ensure that the open source Flink V1.11 is used.
-   For information about upstream and downstream storage or connectors supported by SQL Editor, see [Upstream and downstream storage](/intl.en-US/Fully managed Flink/Overview/Upstream and downstream storage.md).

## Procedure

To help you write and manage Flink SQL jobs, the fully managed Flink service enables a full set of Flink SQL features to make job development efficient. For example, you can manage metadata, register user-defined functions \(UDFs\), and use SQL Editor.

In the development console, perform the following steps to develop a Flink SQL job: Register tables \> Write SQL statements \> Validate SQL statements \> Execute SQL statements.

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

            **Note:** Before you use a UDF in an SQL statement, register the UDF. Only the registered UDFs can be used. For information about how to register a UDF, see [Manage UDFs]().

        6.  Click **Validate**.
        7.  Click **Execute**.

            In the **Tables** list, the table that is registered appears. You can click the table name to view the table fields and data types.

4.  On the **Scripts** tab, click **New Script**.

5.  Write the SQL statements based on the business logic.

    You can write a statement by following the simple logic: `INSERT INTO Result table SELECT xxx FROM Source table;`.

    **Note:** You can create text boxes for DDL statements and data manipulation language \(DML\) statements in a separate way. You can also use the keyword CREATE TEMPORARY TABLE in the DDL script that defines the source table and result table. In this case, you can include the DDL statements and DML statements in a script. This simplifies the operations.

6.  Click **Save**, enter a script name, and then click **Confirm**.

7.  Click **Validate**.

    In the lower part of the SQL script editing page, you can view the verification result of the SQL syntax. Only SQL scripts that have passed the verification can be executed.

8.  Click **Execute**.

9.  In the dialog box that appears, click **OK**.

    ![Deployment required](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7596604161/p133808.png)

    By default, if a DDL script contains the keyword `INSERT INTO`, the system creates a Flink SQL job. After you click **OK**, you are redirected to the job configuration page.

    To save the SQL script, click **Save**. Then, you can view the saved SQL script in the **SQL Scripts** section.


