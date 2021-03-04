# Manage custom connectors

This topic describes how to manage custom connectors in fully managed Flink, including how to create, update, and delete custom connectors.

When you use Flink SQL to develop jobs, you must use SQL connectors to connect your source tables, result tables, and dimension tables. Fully managed Flink supports various types of connectors that are commonly used on the cloud. However, these connectors account for only a small part compared with frequently-used technology stacks of big data. Fully managed Flink allows you to customize connectors and upload them for use so that you can use more types of connectors. When you use connectors, take note of the following items:

-   You must develop your custom connectors based on the connector standard that is defined by the Apache Flink community. This way, the custom connectors can be recognized and used by fully managed Flink. Therefore, you must clearly define the Connector Meta file and declare the Factory class.
-   If you submit custom connectors of a type that is already built in fully managed Flink, the custom connectors that you submit are used to start jobs. If you delete the custom connectors in the future, the built-in connectors of fully managed Flink are used.
-   Connectors of the same type can be uploaded only once.

## Create a custom connector

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, choose **Applications** \> **SQL Editor**.

4.  Click the **Connectors** tab.

5.  Click the ![Plus sign](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8013484161/p187440.png) icon.

    ![Add a connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7055434161/p187441.png)

6.  Upload the JAR file of the custom connector.

    ![Upload the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7055434161/p187443.png)

    You can use one of the following methods to upload the JAR file of the custom connector:

    -   **Upload a file**: Click **Click to Select** and select the desired JAR file of the connector.
    -   **External URL**: Enter an external URL.
7.  After the file is uploaded, click **Continue**.

    The system resolves the uploaded custom connector content. If the resolution is successful, you can proceed to the next step. If the resolution fails, check whether the uploaded custom connector code complies with the standards of the Apache Flink community.

8.  Click **Finish**.

    The created custom connector appears in the Connectors list.


## Update a custom connector

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, choose **Applications** \> **SQL Editor**.

4.  Click the **Connectors** tab.

5.  In the **Connectors** list, move the pointer over the desired custom connector name and click ![Update the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164582.png).

6.  Upload the JAR file of the custom connector.

    ![Modify the connector](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9013484161/p187445.png)

    You can use one of the following methods to upload the JAR file of the custom connector:

    -   **Upload a file**: Click **Click to Select** and select the desired JAR file of the connector.
    -   **External URL**: Enter an external URL.
7.  After the file is uploaded, click **Continue**.

    The system resolves the uploaded custom connector content. If the resolution is successful, you can proceed to the next step. If the resolution fails, check whether the uploaded custom connector code complies with the standards of the Apache Flink community.

8.  Click **Finish**.

    The created custom connector appears in the Connectors list.


## Delete a custom connector

If your custom connector is no longer used, you can perform the following steps to delete the custom connector:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, choose **Applications** \> **SQL Editor**.

4.  Click the **Connectors** tab.

5.  In the **Connectors** list, move the pointer over the desired custom connector name and click the ![Delete the JAR file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8264654161/p164586.png) icon.

6.  Click **Confirm**.


