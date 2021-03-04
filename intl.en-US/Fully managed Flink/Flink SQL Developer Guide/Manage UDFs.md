# Manage UDFs

This topic describes how to manage user-defined functions \(UDFs\) of fully managed Flink, including how to register, update, and delete them.

## Register a UDF

If your SQL statement requires a UDF, you must register your UDF before you use it in the SQL statement.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **SQL Editor**.

4.  Click the **Artifacts** tab.

5.  In the upper-left corner of the page, click the ![Add](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9674654161/p164575.png) icon.

6.  Upload a UDF JAR file.

    ![Upload a JAR file.](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9645434161/p133485.png)

    You can use one of the following methods to upload the UDF JAR file:

    -   **Upload a file**: Click **click to select** and select the desired UDF JAR file.
    -   **External URL**: Enter an external URL.
    **Note:** Your UDF JAR file is uploaded to the artifacts directory in the Object Storage Service \(OSS\) bucket that you select. In addition, the development console resolves whether the classes of the Flink UDF, user-defined aggregation function \(UDAF\), and user-defined table function \(UDTF\) APIs are used in your UDF JAR file. If they are used, the development console automatically extracts the class names and fills them into the Function Name field.

7.  Click **Confirm**.

    In the **Artifacts** list on the left side of the SQL Editor page, you can view all the UDFs that have been registered.


## Update a UDF JAR file

If a UDF is added or the code of the class of the registered UDF is changed in your UDF JAR file, you can perform the following steps to update the UDF JAR file:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **SQL Editor**.

4.  Click the **Artifacts** tab.

5.  In the **UDF JARs** list, move the pointer over the desired JAR file name and click ![Update the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164582.png).

6.  Upload the JAR file.

    ![Update the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9645434161/p224239.png)

    You can use one of the following methods to upload the JAR file:

    -   **Upload a file**: Click **click to select** and select the desired UDF JAR file.
    -   **External URL**: Enter an external URL.
    **Note:**

    -   The new UDF JAR file that you upload must contain all the classes of UDFs that have been registered. The class of a UDF that has been registered cannot be missing.
    -   The code in the new JAR file is used only when you restart a job or submit a new job. If the job that references the UDF JAR file is running, the old JAR file is still used.
7.  Click **Update**.


## Delete a UDF JAR file

If your UDF JAR file is no longer used, you can perform the following steps to delete the UDF:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, click **SQL Editor**.

4.  Click the **Artifacts** tab.

5.  In the **UDF JARs** list, move the pointer over the desired JAR file name and click the ![Delete the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164586.png) icon.

    **Note:** Before you delete the JAR file, make sure that the registered UDFs in the file are not referenced by jobs or SQL files.

6.  Select **Delete associated JAR files.**

    If you need to delete the JAR file, you must delete all the registered UDFs in the JAR file to avoid the residue of dirty data.

7.  Click **Confirm**.


