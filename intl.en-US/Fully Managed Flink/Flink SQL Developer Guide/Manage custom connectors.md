# Manage custom connectors

This topic describes how to manage custom connectors in fully managed Flink. For example, you can create, update, and delete a connector.

When you use Flink SQL to develop jobs, you must use SQL connectors to connect your source tables, result tables, and dimension tables. Fully managed Flink supports multiple types of common connectors in the cloud. However, compared with the technology stacks that are commonly used for big data, fully managed Flink covers only a small part of connectors. If you want to use more types of connectors, you can customize connectors and upload them for use. When you use connectors, take note of the following points:

-   You must develop custom connectors based on the connector standards that are defined by the Apache Flink community. This way, the custom connectors can be recognized and used by fully managed Flink. You must explicitly define connector metadata files and declare factory classes. For more information about how to develop custom connectors, see [User-defined Sources & Sinks](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/table/sourceSinks.html).
-   You can upload one type of connector only once.

To avoid JAR dependency conflicts, take note of the following points:

-   Make sure that the version of the Flink image is the same as the Flink version in the Project Object Model \(POM\) dependencies.
-   Do not upload JAR files at the runtime layer. This indicates that you need to add `<scope>provided</scope>` to the dependencies.
-   Use the Shade plug-in to package third-party dependencies. For more information, see [Apache Maven Shade plug-in](https://maven.apache.org/plugins/maven-shade-plugin/index.html).

## Create a custom connector

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  On the **Connectors** tab, click the ![Plus sign](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8013484161/p187440.png) icon next to Connectors.

    ![Add a connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3174481261/p245238.png)

5.  Upload a JAR file of a custom connector.

    ![Upload a JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7055434161/p187443.png)

    You can use one of the following methods to upload the JAR file of a custom connector:

    -   **Upload a file**: Click **Click to Select** and select the connector JAR file that you want to upload.
    -   **External URL**: Enter an external URL.
6.  After you upload the file, click **Continue**.

    The system parses the content of the file that you upload. If the file parsing succeeds, you can proceed to the next step. If the file parsing fails, you must check whether the code of your custom connector complies with the standards that are defined by the Apache Flink community.

7.  Click **OK**.

    In the Connectors list, the custom connector that you create appears.


## Update a custom connector

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  In the **Connectors** list, move the pointer over the name of the custom connector that you want to update and click the ![Update a JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5162152261/p164582.png) icon.

5.  Upload the JAR file of the custom connector.

    ![Modify a connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9013484161/p187445.png)

    You can use one of the following methods to upload the JAR file of a custom connector:

    -   **Upload a file**: Click **Click to Select** and select the connector JAR file that you want to upload.
    -   **External URL**: Enter an external URL.
6.  After you upload the file, click **Continue**.

    The system parses the content of the file that you upload. If the file parsing succeeds, you can proceed to the next step. If the file parsing fails, you must check whether the code of your custom connector complies with the standards that are defined by the Apache Flink community.

7.  Click **OK**.

    In the Connectors list, the custom connector that you create appears.


## Delete a custom connector

If you do not want to use a custom connector, perform the following steps to delete it:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  In the **Connectors** list, move the pointer over the name of the custom connector that you want to delete and click the ![Delete a JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164586.png) icon.

5.  Click **Confirm**.


