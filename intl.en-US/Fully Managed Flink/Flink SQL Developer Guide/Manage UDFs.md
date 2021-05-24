# Manage UDFs

This topic describes how to manage user-defined functions \(UDFs\) in fully managed Flink. For example, you can register, update, and delete a UDF.

## Usage notes

To avoid JAR package dependency conflicts, take note of the following points:

-   Make sure that the version of the Flink image is the same as the Flink version in the project object model \(POM\) dependencies.
-   Do not upload a JAR package at the runtime layer. This indicates that you need to add `<scope>provided</scope>` to the dependencies.
-   Use the Shade plug-in to package third-party dependencies. For more information, see [Apache Maven Shade plug-in](https://maven.apache.org/plugins/maven-shade-plugin/index.html).

## Register a UDF

Before you can use a UDF in SQL statements, register the UDF. Only registered UDFs can be used in SQL statements.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  Click UDFs tab. In the upper-left corner of the page, click the ![New](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9674654161/p164575.png) icon.

5.  Upload a UDF JAR file.

    ![Register a UDF](../images/p263835.png)

    You can use one of the following methods to upload a UDF JAR file:

    -   **Upload a file**: In the Register UDF Artifact dialog box, click **click to select** that is on the right side of **Select a file** to upload the UDF Artifact file that you want to upload. If you want to upload a dependency file, click **click to select** on the right side of **Dependencies** to upload the file that your UDF Artifact file depends on.
    -   **External URL**: Enter an external URL.
    **Note:**

    -   Your UDF JAR file is uploaded and stored in the sql-artifacts directory of the Object Storage Service \(OSS\) bucket that you select. In the development console of fully managed Flink, the system parses the UDF JAR file and checks whether the classes of the Flink UDF, UDAF, and UDTF interfaces are used in the file. Then, the system automatically extracts the class names and fills them in the Function Name field.
    -   The dependencies of Java UDFs can be added to UDF JAR packages or uploaded by using a dependency file. We recommend that you upload the dependencies of Python UDFs by separately uploading the dependency file.
    -   If the size of the UDF Artifact file or that of the UDF Artifact dependency file is large, we recommend that you upload UDF JAR files by using an external URL. If the external URL is the location of an OSS bucket, the dependency file of the UDF Artifact file must be stored in the sql-artifacts/namespaces/\{namespace\} directory.
6.  Click **Confirm**.

    In the **UDFs** list on the left side of the SQL Editor page, you can view all the UDFs that have been registered.


## Update a UDF

If you add a UDF to a UDF JAR file or change the code of a registered UDF in the file, you can perform the following operations to update the UDF JAR file:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  In the **UDFs** list, move the pointer over the name of the UDF JAR file that you want to update and click the ![Update a UDF JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164582.png) icon.

5.  In the dialog box that appears, upload a UDF JAR file.

    ![Update a UDF JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6480481261/p263828.png)

    You can use one of the following methods to upload a UDF JAR file:

    -   **Upload a file**: Click **click to select** that is on the right side of **Select a file** to upload the UDF Artifact file that you want to upload. If you want to upload a dependency file, click **click to select** on the right side of **Dependencies** to upload the file that your UDF Artifact file depends on.
    -   **External URL**: Enter an external URL.
    **Note:**

    -   The UDF JAR file that you upload must contain all the classes of the registered UDFs.
    -   The code in the new UDF JAR file takes effect only when you restart the job or publish a new job. It does not take effect on a running job. The running job still use the original UDF JAR file.
6.  Click **Update**.


## Delete a UDF

If you do not want to use a UDF JAR file, perform the following operations to delete the UDF JAR file:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  In the **UDFs** list, move the pointer over the name of the UDF JAR file that you want to delete and click the ![Delete a UDF JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164586.png) icon.

    **Note:** Before you delete a UDF JAR file, make sure that the UDF registered by the UDF JAR file is not referenced by a job or an SQL file.

5.  Select **Drop all functions and delete associated UDF JAR files**.

    If you want to delete the UDF JAR file, you must delete all the registered UDFs from the file to avoid dirty data.

6.  Click **Confirm**.


