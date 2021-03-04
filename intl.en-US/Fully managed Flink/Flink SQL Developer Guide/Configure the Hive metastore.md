# Configure the Hive metastore

This topic describes how to configure the Hive metastore in fully managed Flink.

You can store the configuration file of the Hive metastore and Hadoop dependencies to the directories that you specify in the Object Storage Service \(OSS\) console, and configure the Hive metastore in the development console of fully managed Flink. This allows you to use data manipulation language \(DML\) statements to create business logic and obtain metadata from Hive tables in the development console of fully managed Flink. This way, you do not need to use data definition language \(DDL\) statements to declare table information. Take note of the following points before you use the Hive metastore:

-   The version of the Hive metastore must be 2.3.6.
-   The Hive metastore does not support Kerberos authentication.
-   A fully managed Flink instance supports only one Hive metastore. You cannot configure multiple Hive metastore services for multiple projects.
-   The Hive metastore is read-only. This indicates that you cannot create physical tables in Hive through the development console of fully managed Flink.

## Configure the Hive metastore

1.  Enable virtual private cloud \(VPC\) connection between a Hadoop cluster and a fully managed Flink cluster.

    You can use Alibaba Cloud DNS PrivateZone to enable VPC connection between a Hadoop cluster and a fully managed Flink cluster. For more information, see [Resolver](t1930185.md#). This way, you can connect the fully managed Flink cluster to the Hadoop cluster by using the configuration file of the Hadoop cluster.

2.  Log on to the [OSS console](https://oss.console.aliyun.com/).

3.  In the left-side navigation pane, click **Buckets**.

4.  Click the name of the bucket that you want to use.

5.  In the left-side navigation pane, click **Files**.

6.  Create a folder to store the Hive configuration file and Hadoop dependencies.

    -   File path to store the Hive configuration file: oss://$\{bucket\}/artifacts/namespaces/$\{ns\}/$\{hms\}/hive-conf-dir/
    -   Path to store Hadoop dependencies: oss://$\{bucket\}/artifacts/namespaces/$\{ns\}/$\{hms\}/hadoop-conf-dir/
    The following list describes the parameters in the paths:

    -   $\{bucket\}: the name of the bucket that is used by the fully managed Flink instance.
    -   $\{ns\}: the project name that you use in fully managed Flink to configure the Hive metastore feature.
    -   $\{hms\}: the name of the Hive metastore that appears in fully managed Flink.
7.  Store the Hive configuration file hive-site.xml to the directory hive-conf-dir.

8.  Store the following configuration files to the directory hadoop-conf-dir:

    -   hive-site.xml
    -   core-site.xml
    -   hdfs-site.xml
    -   mapred-site.xml
    -   Other files, such as the compressed packages used by Hive jobs.
9.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

10. On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

11. In the left-side navigation pane, click **SQL Editor**.

12. Click the **Scripts** tab.

13. In the upper-left corner of the page, click **New Script**.

14. In the script editing section, enter a statement to create the Hive metastore.

    ```
    CREATE CATALOG ${HMS Name} WITH (
        'type' = 'hive',
        'default-database' = 'default',
        'hive-version' = '2.3.6',
        'hive-conf-dir' = '<hive-conf-dir>'
        'hadoop-conf-dir' = '<hadoop-conf-dir>'
    );
    ```

    The following list describes the parameters in the statement:

    -   $\{HMS Name\}: the name of the Hive metastore.
    -   type: the type of the connector. Set the value to hive.
    -   default-database: the name of the default database.
    -   hive-version: the version of the Hive metastore.
    -   hive-conf-dir: the directory that stores the Hive configuration file.
    -   hadoop-conf-dir: the directory that stores Hadoop dependencies.
15. Click **Execute**.

    After the Hive metastore is configured, you can reference tables in the Hive metastore as a result table and a dimension table in a job. This way, you do not need to use DDL statements to declare tables. Table names in the Hive metastore follow the format $\{hive-catlog-name\}.$\{hive-db-name\}.$\{hive-table-name\}.

    You can also stop the Hive metastore service. For more information, see [Delete the Hive metastore](#section_h4x_5t3_488).


## View Hive metadata

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **SQL Editor**.

4.  Click the **Schemas** tab.

5.  In the top navigation bar, select the Hive metastore that you want to use from the drop-down list.

    ![Hive](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4796604161/p177086.png)

6.  In the **Tables** list, view the tables and fields in different databases.


## Delete the Hive metastore

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to enter, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **SQL Editor**.

4.  Click the **Scripts** tab.

5.  In the upper-left corner of the page, click **New Script**.

6.  In the script editing section, enter the following command:

    `Drop CATALOG ${HMS Name}`

    In the preceding command, HMS Name indicates the name of the Hive metastore that you want to delete. The name appears in the development console of fully managed Flink.

    **Note:** The deletion of the Hive metastore does not affect the running jobs. However, the jobs that are not submitted or need to be suspended and then resumed are affected. Proceed with caution.

7.  Click **Execute**.


