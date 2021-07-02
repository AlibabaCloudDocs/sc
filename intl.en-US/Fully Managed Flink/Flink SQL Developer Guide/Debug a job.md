# Debug a job

This topic describes how to enable job debugging to simulate job running, check outputs, and verify the business logic of SELECT or INSERT statements. This makes development efficient and reduces the risks of poor data quality.

A session cluster for SQL preview is created. Before you debug a job, the system checks whether a session cluster that allows SQL preview exists. If such a session cluster does not exist, the system prompts you to create a session cluster and guides you through the configuration of a session cluster that allows SQL preview. For more information about how to configure a session cluster, see [Configure a session cluster](/intl.en-US/Fully Managed Flink/Configure a session cluster.md).

When you use the SQL preview feature, you can use online data or specify debugging data. You can debug complex jobs that include multiple SELECT or INSERT statements. SQL statements allow you to use UPSERT statements to execute statements that contain update operations, such as count\(\*\).

1.  Go to the Draft Editor page and create an SQL job.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Draft Editor**.

    4.  Click **New**.

    5.  In the dialog box that appears, enter the job configuration information and click **OK**.

        You must select STREAM / SQL for Type and Per-Job Clusters for Deployment Target.

2.  Create a source table and a result table separately.

    Write SQL code for the source table and the result table and click **Execute**. The created tables appear in the Tables list of the Schemas tab. You can click the name of a specific table to view the table fields and data types. For example, you can execute the following statements to create the datagen source table, blackhole result table, and blackhole\_cnt result table.

    -   Create the datagen source table

        ```
        CREATE TABLE `vvp`.`default`.`datagen` (
          `id`   VARCHAR(2147483647),
          `name` VARCHAR(2147483647)
        ) WITH (
          'connector' = 'datagen',
          'rows-per-second' = '5'
        );                        
        ```

    -   Create the blackhole result table

        ```
        CREATE TABLE `vvp`.`default`.`blackhole` (
          `id` VARCHAR(2147483647),
          `name` VARCHAR(2147483647)
        ) WITH (
          'connector' = 'blackhole'
        );                        
        ```

    -   Create the blackhole\_cnt result table

        ```
        CREATE TABLE `vvp`.`default`.`blackhole_cnt` (
          `cnt` BIGINT
        ) WITH (
          'connector' = 'blackhole'
        );                    
        ```

3.  Write Data Manipulation Language \(DML\) statements that are used to debug the job.

    You can execute the following statements to write the data that is read from the datagen source table directly to the blackhole result table, and write the total number of data records in the data to the blackhole\_cnt result table:

    ```
    BEGIN STATEMENT SET;
    
    insert into blackhole
      select *
        from datagen;
    
    insert into `blackhole_cnt`
      select count(*)
        from `datagen`;
    
    END;
    ```

4.  Click **Validate**.

    In the lower part of the SQL script editor, you can view the verification result of the SQL syntax. Only SQL scripts that pass the verification can be executed.

5.  Click **Execute**.

6.  Configure debugging data.

    If you use online data, click **Execute**. If you need to use debugging data, you must upload it first.

    ![Configure debugging data](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8764481261/p245526.png)

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Download Mock Data Template**|You can download the debugging data template to edit data. The template is adapted to the data structure of the source table.|
    |**Upload mock data**|If you need to debug a job by using local debugging data, you can download the debugging data template, upload your on-premises data after you edit it, and then select **Use Mock data**. Limits on debugging data files:

    -   Only CSV files can be uploaded for debugging.
    -   A CSV file must contain a table header, for example, id\(INT\).
    -   A CSV file cannot be greater than 1 MB or can contain a maximum of 1,000 data records. |
    |**Preview data**|After you upload the debugging data, click the ![Plus sign](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8764481261/p245558.png) icon on the left side of the source table name to preview the data and download the debugging data.|
    |**Execution Code Preview**|The job debugging feature automatically modifies the Data Definition Language \(DDL\) code in source tables and result tables. However, this feature does not change the actual code in jobs. You can preview code details in the lower part of Execution Code Preview.|

7.  Click **Execute**.

    The debugging results appear in the lower part of the SQL script editor.

    ![Results](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7007604161/p187809.png)

    **Note:** By default, fully managed Flink reads a maximum of 1,000 data records. If the maximum value is reached, fully managed Flink stops reading data.


