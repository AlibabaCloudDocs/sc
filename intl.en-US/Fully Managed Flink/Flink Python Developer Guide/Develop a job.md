---
keyword: [fully managed Flink, development, Python API]
---

# Develop a job

This topic describes the background information, limits, methods, and connector usage of Python API job development in fully managed Flink.

## Background information

The following table lists the software packages that are installed in fully managed Flink clusters.

|Software package|Version|
|----------------|-------|
|apache-beam|2.23.0|
|avro-python3|1.9.1|
|certifi|2020.12.5|
|chardet|4.0.0|
|cloudpickle|1.2.2|
|crcmod|1.7|
|cython|0.29.16|
|dill|0.3.1.1|
|docopt|0.6.2|
|fastavro|0.23.6|
|future|0.18.2|
|grpcio|1.29.0|
|hdfs|2.6.0|
|httplib2|0.17.4|
|idna|2.10|
|jsonpickle|1.2|
|mock|2.0.0|
|numpy|1.19.5|
|oauth2client|3.0.0|
|pandas|0.25.3|
|pbr|5.5.1|
|pip|20.1.1|
|protobuf|3.15.3|
|py4j|0.10.8.1|
|pyarrow|0.17.1|
|pyasn1-modules|0.2.8|
|pyasn1|0.4.8|
|pydot|1.4.2|
|pymongo|3.11.3|
|pyparsing|2.4.7|
|python-dateutil|2.8.0|
|pytz|2021.1|
|requests|2.25.1|
|rsa|4.7.2|
|setuptools|47.1.0|
|six|1.15.0|
|typing-extensions|3.7.4.3|
|urllib3|1.26.3|
|wheel|0.36.2|

## Limits

Services provided by fully managed Flink are subject to deployment environments and network environments. Therefore, when you develop Python jobs in fully managed Flink, take note of the following limits:

-   Only Apache Flink 1.12 is supported.
-   Python 3.7.9 is pre-installed in fully managed Flink clusters, and common Python libraries such as pandas, NumPy, and PyArrow are pre-installed in the Python environment. Therefore, you must develop code in Python 3.7.
-   Java Development Kit \(JDK\) 1.8 is used in the running environment of fully managed Flink. If your Python job depends on a third-party JAR package, make sure that the JAR package is compatible with JDK 1.8.
-   Only open source Scala 2.11 is supported. If your Python job depends on a third-party JAR package, make sure that the JAR package that is compatible with Scala 2.11 is used.

## Develop a job

Before you submit Python API jobs to clusters for running in the development console of fully managed Flink, develop the jobs in your on-premises environment. You can develop business code in fully managed Flink. For more information, see the following references:

-   For more information about how to develop business code in Apache Flink 1.12, see [Python API](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/python/).
-   Issues may occur when you develop code in Apache Flink. For more information about the issues and fixes, see [FAQ](https://flink.apache.org/gettinghelp.html).

## Debug a job

In the code implementation of Python user-defined extensions \(UDXs\), you can use the logging method to generate logs and locate errors based on the logs. The following code shows an example.

```
@udf(result_type=DataTypes.BIGINT())
def add(i, j):    
  logging.info("hello world")    
  return i + j
```

After logs are generated, you can view the logs in the log file of TaskManager.

## Use a connector

For more information about the connectors supported by fully managed Flink, see [Upstream and downstream storage](/intl.en-US/Fully Managed Flink/Overview/Upstream and downstream storage.md). To use a connector, perform the following steps:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).
2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Development Console** in the **Actions** column.
3.  In the left-side navigation pane, click **Artifacts**.
4.  Click **Upload Artifact** and select the JAR package of the connector that you want to use.

    You can upload the JAR package of your self-managed connector or the JAR package of a connector provided by fully managed Flink. For the download links of the official JAR packages provided by fully managed Flink, see [Connector list](https://repo1.maven.org/maven2/com/alibaba/ververica/).

5.  In the **Additional Dependencies** section of the Drafts page, select the JAR package of the connector that you want to use.
6.  On the right side of the Drafts page, click the **Advanced** tab and enter the relevant configurations in the **Additional Configuration** section.

    For example, if the job depends on the two JAR packages of a connector that are named connector-1.jar and connector-2.jar, add the following configuration information:

    ```
    pipeline.classpaths: 'file:///flink/usrlib/connector-1.jar;file:///flink/usrlib/connector-2.jar'
    ```


