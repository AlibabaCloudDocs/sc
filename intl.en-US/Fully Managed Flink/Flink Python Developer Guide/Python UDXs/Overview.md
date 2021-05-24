---
keyword: [Python UDX, classification, definition, debugging, performance optimization, dependency]
---

# Overview

Fully managed Flink allows you to use Python user-defined extensions \(UDXs\) in SQL jobs. This topic describes the classification, definition, and dependencies of Flink UDXs. It also describes how to debug jobs and optimize job performance.

## UDX types

The following table describes the three types of UDXs that are supported by Realtime Compute for Apache Flink.

|UDX type|Description|
|--------|-----------|
|User-defined scalar function \(UDF\)|A UDF maps zero, one, or more scalar values to a new scalar value. A one-to-one mapping is established between the input and output of a UDF. Each time a UDF reads a row of data, it writes an output value. For more information, see [Python UDFs]().|
|User-defined aggregate function \(UDAF\)|A UDAF aggregates multiple values into a single value. A many-to-one mapping is established between the input and output of a UDAF. Multiple input records are aggregated to generate one output value. For more information, see [t2069975.md\#]().|
|User-defined table-valued function \(UDTF\)|A UDTF takes zero, one, or more scalar values as input parameters. These parameters can be variable-length parameters. UDTFs are similar to UDFs but differ in that they return any number of rows instead of a single value. Returned rows consist of one or more columns. Multiple rows or columns are returned each time a UDTF is called. For more information, see [Python UDTFs]().|

## Dependencies

Commonly used Python packages such as pandas, NumPy, and PyArrow are pre-installed in fully managed Flink. You can view a list of third-party Python packages that are pre-installed in fully managed Flink on the [Develop a job](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Develop a job.md) page.

You can also use other types of third-party Python packages in Python UDXs. If you use a third-party Python package that is not pre-installed in fully managed Flink, you must upload the package as a dependency file to fully managed Flink. For more information, see [Manage UDFs](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Manage UDFs.md) and [Use Python dependencies](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Use Python dependencies.md).

## Debugging

In the code implementation of Python UDXs, you can use the logging method to generate logs and locate errors based on the logs. The following code is an example.

```
@udf(result_type=DataTypes.BIGINT())
def add(i, j):    
  logging.info("hello world")    
  return i + j
```

After logs are generated, you can view the logs in the log file of TaskManager.

## Performance optimization

-   Use the pandas library

    Fully managed Flink allows you to use pandas UDXs in addition to common Python UDXs. The input data types of pandas UDXs use the data structures defined in pandas, such as pandas.Series and pandas.DataFrame. You can use high-performance Python libraries such as pandas and NumPy in pandas UDXs to develop high-performance Python UDXs. For more information, see [Vectorized User-defined Functions](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/python/table-api-users-guide/udfs/vectorized_python_udfs.html).

-   Configure required parameters

    The performance of Python UDXs mainly depends on their implementation. If you encounter performance issues related to Python UDXs, you need to optimize their implementation. The performance of Python UDXs is also affected by the values of the parameters described in the following table.

    |Parameter|Description|
    |---------|-----------|
    |python.fn-execution.bundle.size|Python UDFs are asynchronously calculated. During calculation, a Java operator asynchronously sends data to a Python process for processing. The Java operator caches data and then sends the data to the Python process when the number of data records in the cache reaches a specific threshold. The maximum number of data records that can be cached. Default value: 100000. |
    |python.fn-execution.bundle.time|The maximum duration for which data can be cached. If the number of cached data reaches the value specified by the python.fn-execution.bundle.size parameter or the duration for which data is cached reaches the value specified by the python.fn-execution.bundle.time parameter, the calculation on the cached data is triggered. Default value: 1000. Unit: milliseconds. |
    |python.fn-execution.arrow.batch.size|The maximum number of data records that are allowed in an arrow batch when you use pandas UDFs. Default value: 10000. **Note:** The value of the python.fn-execution.arrow.batch.size parameter cannot be greater than the value of the python.fn-execution.bundle.size parameter. |

    **Note:** If the values of these parameters are too large, excessive data needs to be processed during checkpointing. This prolongs checkpointing or even causes a failure of checkpointing. For more information about these parameters, see [Configuration](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/python/python_config.html).


