---
keyword: [UDAF, Python UDAF]
---

# Python UDAFs

This topic describes how to develop, register, and use a user-defined aggregate function \(UDAF\) in Realtime Compute for Apache Flink.

## Definition

A UDAF aggregates multiple values into a single value. A many-to-one mapping is established between the input and output of a UDAF. Multiple input records are aggregated to generate one output value.

## Develop a UDAF

**Note:** Flink provides sample code of Python user-defined extensions \(UDXs\) for you to develop UDXs. The sample code includes the implementation of Python UDFs, Python UDAFs, and Python user-defined table-valued functions \(UDTFs\). This topic describes how to develop a UDAF in the Windows operating system.

1.  Double-click the udfs.py file in the \\python\_demo-master\\udx directory. Then, modify the content of the file based on your business requirements.

    In this example, weighted\_avg defines the code for the weighted average of the current data and historical data.

    ```
    from pyflink.common import Row
    from pyflink.table import AggregateFunction, DataTypes
    from pyflink.table.udf import udaf
    
    
    class WeightedAvg(AggregateFunction):
    
        def create_accumulator(self):
            # Row(sum, count)
            return Row(0, 0)
    
        def get_value(self, accumulator: Row) -> float:
            if accumulator[1] == 0:
                return 0
            else:
                return accumulator[0] / accumulator[1]
    
        def accumulate(self, accumulator: Row, value, weight):
            accumulator[0] += value * weight
            accumulator[1] += weight
    
        def retract(self, accumulator: Row, value, weight):
            accumulator[0] -= value * weight
            accumulator[1] -= weight
    
    
    weighted_avg = udaf(f=WeightedAvg(),
                        result_type=DataTypes.DOUBLE(),
                        accumulator_type=DataTypes.ROW([
                            DataTypes.FIELD("f0", DataTypes.BIGINT()),
                            DataTypes.FIELD("f1", DataTypes.BIGINT())]))
    ```

2.  Go to the \\python\_demo-master directory to which the udx folder belongs and run the following command to package the files in the directory:

    ```
    zip -r python_demo.zip udx
    ```

    If the python\_demo.zip package appears in the \\python\_demo-master\\ directory, the UDAF is developed.


## Register a UDAF

For more information about how to register a UDAF, see [.](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Manage UDFs.md)

## Use a UDAF

After a UDAF is registered, you can perform the following steps to use it:

1.  Use Flink SQL to create a job. For more information, see [Develop a job](/intl.en-US/Fully Managed Flink/Flink Python Developer Guide/Develop a job.md).

    Obtain the average value of the current data and historical data of field a in the ASI\_UDAF\_Source table with field b as the weight. The following code shows an example.

    ```
    CREATE TEMPORARY TABLE ASI_UDAF_Source (
      a   BIGINT,
      b   BIGINT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDAF_Sink (
      avg_value  DOUBLE
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDAF_Sink
    SELECT weighted_avg(a, b)
    FROM ASI_UDAF_Source;
    ```

2.  On the **Deployments** page, find the job for which you want to use the UDAF and click **Start** in the **Actions** column.

    After the job is started, the average of the current data and historical data of the a field in the ASI\_UDAF\_Source table is inserted into each row in the ASI\_UDAF\_Sink table with field b as the weight.


