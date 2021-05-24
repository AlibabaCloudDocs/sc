---
keyword: [UDF, Python UDF]
---

# Python UDFs

This topic describes how to develop, register, and use a user-defined scalar function \(UDF\).

## Definition

A UDF maps zero, one, or more scalar values to a new scalar value. A one-to-one mapping is established between the input and output of a UDF. Each time a UDF reads a row of data, it writes an output value.

## Limits

Services provided by fully managed Flink are subject to deployment environments and network environments. Therefore, when you develop Python UDFs in fully managed Flink, take note of the following limits:

-   Only Apache Flink 1.12 and later versions are supported.
-   Python 3.7.9 is pre-installed on a fully managed Flink cluster. Therefore, you must develop code in Python 3.7.9.
-   JDK 1.8 is used in the running environment of fully managed Flink. If your Python job depends on a third-party JAR package, make sure that the JAR package is compatible with JDK 1.8.
-   Only open source Scala 2.11 is supported. If your Python job depends on a third-party JAR package, make sure that the JAR package that corresponds to Scala 2.11 is used.

## Develop a UDF

**Note:** Flink provides sample code to help you quickly develop Python user-defined extensions \(UDXs\). This sample code includes the implementation of Python UDFs, Python user-defined aggregate functions \(UDAFs\), and Python user-defined table-valued functions \(UDTFs\) This topic describes how to develop a UDF in the Windows operating system.

1.  Download and decompress the [python\_demo-master](https://github.com/RealtimeCompute/python_demo) package to your on-premises machine.
2.  In the main menu bar of PyCharm, choose **File** \> **Open** to open the decompressed python\_demo-master package.
3.  Double-click the udfs.py file in the \\python\_demo-master\\udx directory. Then, modify the content of the file based on your business requirements.

    In this example, sub\_string defines the code to obtain the characters from the begin position to the end position in each data record.

    ```
    from pyflink.table import DataTypes
    from pyflink.table.udf import udf
    
    
    @udf(result_type=DataTypes.STRING())
    def sub_string(s: str, begin: int, end: int):
        return s[begin:end]
    ```

4.  Go to the \\python\_demo directory to which the udx folder belongs and run the following command to package the files in the directory:

    ```
    zip -r python_demo.zip udx
    ```

    If the python\_demo.zip package appears in the \\python\_demo-master\\ directory, the Python UDF is developed.


## Register a UDF

For more information about how to register a UDF, see [Register a UDF](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Manage UDFs.md).

## Use a UDF

After a UDF is registered, you can perform the following steps to use it:

1.  Use Flink SQL to create a job. For more information, see [Develop a job](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Develop a job.md).

    The following code provides an example on how to obtain the characters the second character to the fourth character of the string in each row of the a field in the ASI\_UDF\_Source table:

    ```
    CREATE TEMPORARY TABLE ASI_UDF_Source (
      a VARCHAR,
      b INT,
      c INT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDF_Sink (
      a VARCHAR
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDF_Sink
    SELECT ASI_UDF(a,2,4)
    FROM ASI_UDF_Source;
    ```

2.  On the **Deployments** page, find the job for which you want to use the UDF and click **Start** in the **Actions** column.

    After the job is started, each row of the ASI\_UDF\_Sink table is inserted with the characters from the second character to the fourth character of the string in each row of the a field in the ASI\_UDF\_Source table.


