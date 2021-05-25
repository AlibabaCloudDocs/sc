# Configure the Hive metastore service

This topic describes how to configure the Hive metastore service in fully managed Flink.

You can store configuration files of the Hive metastore and Hadoop dependencies in the specified directory in the Object Storage Service \(OSS\) console. Then, you can configure the Hive metastore service in the development console of fully managed Flink. After you configure the Hive metastore, you can execute Data Manipulation Language \(DML\) statements to create business logic and obtain metadata from Hive tables. You do not need to execute Data Definition Language \(DDL\) statements to declare related table information. The Hive metastore can be used as a source table or result table of streaming jobs and batch jobs.

## Usage notes

Before you use the Hive metastore service, take note of the following points:

-   Self-managed Hive metastore is supported.
-   Ververica Platform \(VVP\) 2.3.0 and later support Hive metastore.
-   VVP 2.3.0 supports only Hive metastore 2.3.6. Versions later than VVP 2.3.0 support Hive metastore 2.2.0 to 2.3.7.
-   The Hive metastore does not support Kerberos authentication.
-   A fully managed Flink instance supports only one Hive metastore. You cannot configure multiple Hive metastores for multiple projects.
-   The Hive metastore is read-only. This indicates that you cannot create physical tables in Hive in the development console of fully managed Flink.

## Configure the Hive metastore service

1.  Establish a connection between a Hadoop cluster and a fully managed Flink instance in a VPC.

    You can use Alibaba Cloud DNS PrivateZone to connect a Hadoop cluster and a fully managed Flink instance in a VPC. For more information, see [Resolver](/intl.en-US/User Guide/Resolver.md). After the connection is established, the fully managed Flink instance can access Hadoop clusters by using the configuration files of Hadoop clusters.

2.  In the OSS console, create folders under the directories in which you want to store the Hive configuration file and Hadoop dependencies.

    1.  Log on to the .

    2.  In the left-side navigation pane, click **Buckets**.

    3.  Click the name of the bucket that you want to manage.

    4.  In the left-side navigation pane, click **Files**.

    5.  Create folders to store the Hive configuration file and Hadoop dependencies.

        -   Path to store the Hive configuration file: oss://$\{bucket\}/artifacts/namespaces/$\{ns\}/$\{hms\}/hive-conf-dir/
        -   Path to store Hadoop dependencies: oss://$\{bucket\}/artifacts/namespaces/$\{ns\}/$\{hms\}/hadoop-conf-dir/
        Parameters in the paths:

        -   $\{bucket\}: the name of the bucket that is used by the fully managed Flink instance.
        -   $\{ns\}: the name of the fully managed Flink project that uses the Hive metastore.
        -   $\{hms\}: the name of the Hive metastore that is displayed in the development console of fully managed Flink.
    6.  Save the Hive configuration file hive-site.xml in the directory hive-conf-dir.

        Before you upload the hive-site.xml file, check whether the parameter hive.metastore.uris in the hive\_site.xml file meets the following requirements.

        ```
        <property>
            <name>hive.metastore.uris</name>
            <value>thrift://xx.yy.zz.mm:9083</value>
            <description>Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.</description>
         </property>
        ```

        `xx.yy.zz.mm` is the internal or public IP address of Hive. If you set hive.metastore.uris to hostname, the value of the hive.metastore.uris parameter fails to be parsed and the error message `UnknownHostException` is returned when VVP remotely accesses Hive.

    7.  Save the following configuration files in the directory hadoop-conf-dir:

        -   hive-site.xml
        -   core-site.xml
        -   hdfs-site.xml
        -   mapred-site.xml
        -   Other files, such as the compressed packages used by Hive jobs
3.  Configure the Hive metastore service in the development console of fully managed Flink.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

    3.  In the left-side navigation pane, choose **Applications** \> **Draft Editor**.

    4.  In the upper-left corner of the page, click **New**. In the New Draft dialog box, select STREAM/SQL from the drop-down list of Type.

    5.  In the script editor, enter a statement to create a Hive metastore.

        ```
        CREATE CATALOG ${HMS Name} WITH (
            'type' = 'hive',
            'default-database' = 'default',
            'hive-version' = '<hive-version>',
            'hive-conf-dir' = '<hive-conf-dir>'
            'hadoop-conf-dir' = '<hadoop-conf-dir>'
        );
        ```

        Parameters in the statement:

        -   $\{HMS Name\}: the name of the Hive metastore.
        -   type: the type of the connector. Set the value to hive.
        -   default-database: the name of the default database.
        -   hive-version: the version of the Hive metastore.

            **Note:** Fully managed Flink is compatible with Hive metastore from 2.2.0 to 2.3.7.

            -   2.0.0 to 2.2.0: If the version of the Hive metastore is 2.0.0 to 2.2.0, the hive-version must be 2.2.0.
            -   2.3.0 to 2.3.7: If the version of the Hive metastore is 2.3.0 to 2.3.7, the hive-version must be 2.3.6.
        -   hive-conf-dir: the directory that stores the Hive configuration file.
        -   hadoop-conf-dir: the directory that stores Hadoop dependencies.
    6.  Click **Execute**.

        After the Hive metastore service is configured, you can reference tables from the Hive metastore as result tables and dimension tables in jobs. You do not need to declare DDL statements for these tables. Table names in the Hive metastore are in the format of $\{hive-catlog-name\}.$\{hive-db-name\}.$\{hive-table-name\}.

        If you want to delete the Hive metastore service, see [Delete the Hive metastore service](#section_h4x_5t3_488).


## View Hive metadata

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  Click the **Schemas** tab.

5.  In the top navigation bar, select the Hive metastore that you want to manage from the drop-down list.

    ![Hive](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5361481261/p177086.png)

6.  Click the **Tables** node and view the tables and fields in different databases.


## Delete the Hive metastore service

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, click **Draft Editor**.

4.  In the upper-left corner of the page, click **New**. In the New Draft dialog box, select STREAM/SQL from the drop-down list of Type.

5.  In the script editor, enter the following command:

    `Drop CATALOG ${HMS Name}`

    In the preceding command, HMS Name indicates the name of the Hive metastore that you want to delete. The name displayed in the development console of fully managed Flink.

    **Note:** The delete operation does not affect the running jobs. However, the jobs that are not published or jobs that need to be suspended and then resumed are affected. Proceed with caution.

6.  Click **Execute**.


