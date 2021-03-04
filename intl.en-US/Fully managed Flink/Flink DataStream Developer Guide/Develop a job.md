---
keyword: [fully managed Flink, development, DataStream, Table API]
---

# Develop a job

This topic describes the limits and methods to develop DataStream API jobs, Table API jobs, and Python API jobs in fully managed Flink.

## Develop a DataStream API job or Table API job

-   Limits

    Services provided by **fully managed Flink** are subject to deployment environments and network environments. Therefore, when you develop DataStream API jobs or Table API jobs in **fully managed Flink**, take note of the following limits:

    -   Only open source Flink V1.10 and Flink V1.11 are supported.
    -   You can submit and run jobs in the only JAR format.
    -   Only a single job in the JAR format can be run at a time.
    -   You cannot read local configurations by using the main\(\) method.
    -   To develop jobs, use JDK 1.8 because **fully managed Flink** is developed by using JDK 1.8.
    -   Only open source Scala V2.11 is supported.
-   Use a connector

    The Ververica Runtime \(VVR\) connector is placed in the Maven central repository. You must add the connector that you want to use to the Maven Project Object Model \(POM\) file that is a dependency for a project. The following example shows the sample code:

    ```
    <dependencies>
        <dependency>
            <groupId>com.alibaba.ververica</groupId>
            <artifactId>${connector.type}</artifactId>
            <version>${connector.version}</version>
        </dependency>
    </dependencies>
    ```

    Different connector versions may correspond to different connector types. We recommend that you use the latest version. The following table lists the mappings among connector versions, VVR/Flink versions, and connector types. You can view the release notes of different versions to obtain details.

    **Note:** You must search for the connector versions that contain the SNAPSHOT keyword from the SNAPSHOT repository oss.sonatype.org. You cannot search for the versions from the Maven central repository search.maven.org.

    |Connector version|VVR/Flink version|Connector type|
    |-----------------|-----------------|--------------|
    |1.11-vvr-2.1.3-SNAPSHOT|VVR 2.1.3 \(corresponds to Flink 1.11.3\)|    -   ververica-connector-common
    -   ververica-connector-random
    -   ververica-connector-datahub
    -   ververica-connector-odps
    -   ververica-connector-cloudhbase
    -   ververica-connector-phoenix
    -   ververica-connector-redis
    -   ververica-connector-elasticsearch
    -   ververica-connector-mongodb
    -   ververica-connector-hologres
    -   ververica-connector-jdbc
    -   ververica-connector-ads
    -   ververica-connector-adb-3.0
    -   ververica-connector-rds
    -   ververica-connector-ots
    -   ververica-connector-elasticsearch6
    -   ververica-connector-elasticsearch7
    -   ververica-connector-kafka
    -   ververica-connector-hadoop-shaded
    -   ververica-connector-cloudhbase-shaded
    -   ververica-connector-mysql
    -   ververica-connector-postgres
    -   ververica-connector-hive-2.3.6
    -   ververica-connector-hive-3.1.2
    -   ververica-connector-continuous-odps |
    |1.11-vvr-2.1.2-SNAPSHOT|VVR 2.1.2 \(corresponds to Flink 1.11.2\)|    -   ververica-connector-common
    -   ververica-connector-random
    -   ververica-connector-datahub
    -   ververica-connector-odps
    -   ververica-connector-cloudhbase
    -   ververica-connector-phoenix
    -   ververica-connector-redis
    -   ververica-connector-elasticsearch
    -   ververica-connector-mongodb
    -   ververica-connector-hologres
    -   ververica-connector-jdbc
    -   ververica-connector-ads
    -   ververica-connector-adb-3.0
    -   ververica-connector-rds
    -   ververica-connector-ots
    -   ververica-connector-elasticsearch6
    -   ververica-connector-elasticsearch7
    -   ververica-connector-kafka
    -   ververica-connector-hadoop-shaded
    -   ververica-connector-cloudhbase-shaded
    -   ververica-connector-mysql
    -   ververica-connector-postgres
    -   ververica-connector-hive-2.3.6
    -   ververica-connector-hive-3.1.2
    -   ververica-connector-continuous-odps |

-   Develop a job

    Before you submit jobs to clusters for running in the development console of **fully managed Flink**, develop the jobs in your on-premises environment. You can develop business code in **fully managed Flink**. For more information, see the following topics:

    -   For information about the Flink DataStream and Table API connector dependencies, see [Connector dependencies](http://oss.sonatype.org/).
    -   For information about the description, architecture, applications, and features of Apache Flink, see [Introduction to Apache Flink](https://flink.apache.org/flink-architecture.html).
    -   For information about how to develop business code in Apache Flink V1.10, see [Flink DataStream API development guide](https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/datastream_api.html) and [Flink Table API and SQL development guide](https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/table/).
    -   For information about the code, Java and Scala language guides, and component and formatting guides, see [Code style and quality guide](https://flink.apache.org/contributing/code-style-and-quality-preamble.html).
    -   Issues may occur when you develop code in Apache Flink. For more information about the issues and fixes, see [FAQ](https://flink.apache.org/gettinghelp.html).
    To avoid JAR dependency conflicts, take note of the following points:

    -   Ensure that the version of the Flink image tag is the same as the Flink version in the POM dependency.
    -   Do not upload JAR files at the runtime layer. This indicates that do not add `<scope>provided</scope>` to dependencies.
    -   Use the Shade plug-in to package other third-party dependencies. For more information, see [Apache Maven Shade plug-in](https://maven.apache.org/plugins/maven-shade-plugin/index.html).

## Develop a Python API job

-   Limits
    -   Only open source Flink V1.11 is supported.
    -   Python 3.7 is pre-installed for a fully managed Flink cluster, and common Python libraries are pre-installed in the Python environment, including Pandas, NumPy, and PyArrow. Therefore, you must edit code in Python 3.7.
-   Develop a job

    Before you submit Python API jobs to clusters for running in the development console of **fully managed Flink**, develop the jobs in your on-premises environment. You can develop business code in **fully managed Flink**. For more information, see the following topics:

    -   For information about how to develop business code in Apache Flink V1.11, see [Flink Python Table API development guide](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/python/table-api-users-guide/intro_to_table_api.html).
    -   Issues may occur when you develop code in Apache Flink. For more information about the issues and fixes, see [FAQ](https://flink.apache.org/gettinghelp.html).

