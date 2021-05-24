---
keyword: [UDTF, Python UDTF]
---

# Python UDTFs

This topic describes how to develop, register, and use a user-defined table-valued function \(UDTF\).

## Definition

A UDTF takes zero, one, or more scalar values as input parameters. These parameters can be variable-length parameters. UDTFs return a number of rows instead of a single value. Returned rows consist of one or more columns. Multiple rows or columns are returned each time a UDTF is called. UDTFs are similar to user-defined scalar functions \(UDFs\) but differ in the results they return.

## Develop a UDTF

**Note:** Flink provides example code to help you quickly develop Python user-defined extensions \(UDXs\). The sample code includes the implementation of Python UDFs, Python user-defined aggregate functions \(UDAFs\), and Python UDTFs. This topic describes how to develop a UDTF in the Windows operating system.

1.  Double-click the udtfs.py file in the \\python\_demo-master\\udx directory. Then, modify the content of the file based on your business requirements.

    In this example, split defines the code that can split a row of string into multiple columns of strings with vertical bars \(\|\).

    ```
    from pyflink.table import DataTypes
    from pyflink.table.udf import udtf
    
    @udtf(result_types=[DataTypes.STRING(), DataTypes.STRING()])
    def split(s: str):
        splits = s.split("|")
        yield splits[0], splits[1]
    ```

2.  Go to the \\python\_demo directory to which the udx folder belongs and run the following command to package the files in the directory:

    ```
    zip -r python_demo.zip udx
    ```

    If the python\_demo.zip package appears in the \\python\_demo\\ directory, the UDTF is developed.


## Register a UDTF

For more information about how to register a UDTF, see [Register a UDF](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Manage UDFs.md).

## Use a UDTF

After you register a UDTF, you can perform the following steps to use it:

1.  Use Flink SQL to create a job. For more information, see [Develop a job](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Develop a job.md).

    After the aa string and the message field in each row of string in the ASI\_UDTF\_Source table are concatenated with vertical bars \(\|\), the concatenated strings are split into multiple columns of strings by vertical bars \(\|\). The following code shows an example:

    ```
    CREATE TEMPORARY TABLE ASI_UDTF_Source (
      `message`  VARCHAR
    ) WITH (
      'connector'='datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDTF_Sink (
      name  VARCHAR,
      place  VARCHAR
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDTF_Sink
    SELECT name,place
    FROM ASI_UDTF_Source,lateral table(split(concat_ws('|', `message`, 'aa'))) as T(name,place);
    ```

2.  On the **Deployments** page, find the job for which you want to use the UDTF and click **Start** in the **Actions** column.

    After the job is started, two columns of data are inserted into the ASI\_UDTF\_Sink table. The two columns of data are the concatenated strings that are separated by vertical bars \(\|\)


