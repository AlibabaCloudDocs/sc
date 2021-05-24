---
keyword: [fully managed Flink, development, DataStream, Table API]
---

# Develop a job

This topic describes the limits and methods to develop DataStream API jobs and Table API jobs in fully managed Flink.

## Limits

Services provided by fully managed Flink are subject to deployment environments and network environments. Therefore, when you develop DataStream API jobs or Table API jobs in fully managed Flink, take note of the following limits:

-   Only open source Flink 1.10, Flink 1.11, and Flink 1.12 are supported.
-   You can publish and run jobs only in the JAR format.
-   You can use one main JAR package and multiple JAR dependencies.
-   You cannot read local configurations by using the main\(\) method.
-   Java Development Kit \(JDK\) 1.8 is used in the running environment. Therefore, you must also use JDK 1.8 for job development.
-   Only open source Scala 2.11 is supported.

## Usage notes

To avoid JAR dependency conflicts, take note of the following points:

-   Make sure that the Flink versions of Flink images and Project Object Model \(POM\) dependencies are the same.
-   Do not upload JAR files at the runtime layer. You must add `<scope>provided</scope>` to dependencies.
-   Use the Shade plug-in to package other third-party dependencies. For more information, see [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/index.html).

## Develop a job

Before you publish jobs to clusters for running in the development console of fully managed Flink, develop the jobs in your on-premises environment. When you write business code of fully managed Flink, see the following references:

-   For more information about the Flink DataStream and Table API connector dependencies, see [Connector dependencies](http://oss.sonatype.org/).
-   For more information about how to use connectors, see [Use a connector](#section_x4p_87g_as0).
-   For more information about the description, architecture, applications, and features of Apache Flink, see [Introduction to Apache Flink](https://flink.apache.org/flink-architecture.html).
-   For more information about how to develop business code in Apache Flink 1.10, see [Flink DataStream API Programming Guide](https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/datastream_api.html) and [Flink Table API and SQL](https://ci.apache.org/projects/flink/flink-docs-release-1.10/dev/table/).
-   For more information about the code, Java and Scala language guides, and component and formatting guides, see [Apache Flink Code Style and Quality Guide](https://flink.apache.org/contributing/code-style-and-quality-preamble.html).
-   Issues may occur when you develop code in Apache Flink. For more information about the issues and fixes, see [FAQ](https://flink.apache.org/gettinghelp.html).

## Use a connector

The Ververica Runtime \(VVR\) connector is placed in the Maven central repository for you to use during job development. You must add the connector that you want to use to the Maven POM file that is a dependency for your project. The following example shows the sample code:

```
<dependencies>
    <dependency>
        <groupId>com.alibaba.ververica</groupId>
        <artifactId>${connector.type}</artifactId>
        <version>${connector.version}</version>
    </dependency>
</dependencies>
```

Different connector versions may correspond to different connector types. We recommend that you use the latest version. The following table lists the mappings among connector versions, VVR or Flink versions, and connector types. You can view the release notes for the new features of different versions to obtain details.

**Note:** You must search for the connector versions that contain the SNAPSHOT keyword from the SNAPSHOT repository oss.sonatype.org. You cannot search for the versions in the Maven central repository search.maven.org.

|Connector version|VVR or Flink version|Connector type|
|-----------------|--------------------|--------------|
|1.11-vvr-2.1.3-SNAPSHOT|VVR 2.1.3 \(corresponds to Flink 1.11.3\)|-   ververica-connector-common
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
|1.11-vvr-2.1.2-SNAPSHOT|VVR 2.1.2 \(corresponds to Flink 1.11.2\)|-   ververica-connector-common
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

