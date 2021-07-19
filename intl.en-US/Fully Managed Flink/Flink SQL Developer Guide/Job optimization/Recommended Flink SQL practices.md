---
keyword: [Flink SQL practice, hotspot, data skew]
---

# Recommended Flink SQL practices

This topic describes how to improve the performance of a Flink SQL job based on the optimization of job configurations and Flink SQL practices.

## Recommended practices for job configuration optimization

-   Recommended practices for resource configuration optimization

    Ververica Platform \(VVP\) imposes limits on the number of CPU cores that can be used in the JobManager and the number of CPU cores that can be used in each TaskManager. The maximum number of CPU cores that can be used equals the number of CPU cores you configured. When you optimize resource configurations, we recommend that you perform the following operations:

    -   If a large number of jobs are running in parallel, you can increase the number of CPU cores and memory size used by the JobManager in **Resources** on the **Advanced** tab. Example:
        -   Set Job Manager CPUs to 4.
        -   Set Job Manager Memory to 8 GiB.
    -   If the job topologies are complex, you can increase the number of CPU cores and memory size used by a TaskManager in **Resources** on the **Advanced** tab. Example:
        -   Set Task Manager CPUs to 2.
        -   Set Task Manager Memory to 4 GiB.
    -   We recommend that you retain the default value of taskmanager.numberOfTaskSlots. The default value is 1.
-   Recommended practices for improving throughput and resolving data hotspot issues

    Add the following code to the **Additional Configuration** section on the **Advanced** tab. For more information, see [Optimization by using group aggregation](#section_211_m3y_k5i).

    ```
    execution.checkpointing.interval: 180s
    state.backend: com.alibaba.flink.statebackend.GeminiStateBackendFactory
    state.backend.gemini.ttl.ms: 129600000
    table.exec.mini-batch.enabled: true
    table.exec.mini-batch.allow-latency: 5s
    table.optimizer.distinct-agg.split.enabled: true
    env.java.opts.taskmanager: -Xms4096m -XX:+UseParNewGC -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -verbose:gc -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSMaxAbortablePrecleanTime=1000 -XX:+CMSClassUnloadingEnabled -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:SurvivorRatio=5 -XX:ParallelGCThreads=4
    ```

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |execution.checkpointing.interval|The checkpoint interval, in milliseconds.|
    |state.backend|The configuration of the state backend.|
    |state.backend.gemini.ttl.ms|The lifecycle of state data, in milliseconds.|
    |table.exec.mini-batch.enabled|Specifies whether to enable miniBatch.|
    |table.exec.mini-batch.allow-latency|The interval at which data is exported at a time.|
    |table.optimizer.distinct-agg.split.enabled|Specifies whether to enable PartialFinal optimization to resolve data hotspot issues when you execute the COUNT DISTINCT function.|
    |env.java.opts.taskmanager|The Java options to be started. **Note:** If a Log Service source table is used, you cannot set this parameter because the setting of this parameter may cause performance bottlenecks. |


## Optimization by using group aggregation

-   Enable miniBatch to improve the throughput

    If miniBatch is enabled, Realtime Compute for Apache Flink processes data when the data cache meets the trigger condition. This reduces the number of times that Realtime Compute for Apache Flink accesses the state data. This improves the throughput and reduces data output.

    The miniBatch feature triggers micro-batch processing based on event messages. The event messages are inserted at the source at a specified interval.

    -   Scenarios

        Micro-batch processing achieves higher throughput at the expense of higher latency. Therefore, micro-batch processing does not apply to scenarios that require extremely low latency. However, in data aggregation scenarios, we recommend that you enable micro-batch processing to improve system performance.

    -   Method to enable miniBatch

        The miniBatch feature is disabled by default. To enable this feature, you must enter the following code in the **Additional Configuration** section of the **Advanced** tab.

        ```
        table.exec.mini-batch.enabled: true
        table.exec.mini-batch.allow-latency: 5s
        ```

        The following table describes the parameters.

        |Parameter|Description|
        |---------|-----------|
        |table.exec.mini-batch.enabled|Specifies whether to enable miniBatch.|
        |table.exec.mini-batch.allow-latency|The interval at which data is exported.|

-   Enable LocalGlobal to resolve common data hotspot issues

    The LocalGlobal policy can filter out some skewed data by using local aggregation and resolve the data hotspot issues in global aggregation. This enhances job performance. The following figure shows how the LocalGlobal policy resolves the data skew issue.

    ![LocalGlobal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2716766261/p262011.png)

    The LocalGlobal policy divides the aggregation process into two phases: local aggregation and global aggregation. These phases are equivalent to the combine and reduce phases in MapReduce. In the local aggregation phase, Realtime Compute for Apache Flink aggregates a micro-batch of data that is locally cached at each upstream node, and generates the accumulator value for each micro-batch. In the global aggregation phrase, the accumulator is merged into the final result. Then, the global aggregation result is generated.

    -   Scenarios

        The LocalGlobal policy is suitable for scenarios in which you want to improve job performance and resolve data hotspot issues by using common aggregate functions such as SUM, COUNT, MAX, MIN, and AVG.

    -   Limits

        The default value of blink.localAgg.enabled is true, which indicates that LocalGlobal is enabled. This policy has the following limits:

        -   It can take effect only when miniBatch is enabled.
        -   AggregateFunction must be used to merge data.
    -   Verification

        To determine whether LocalGlobal is enabled, check whether the GlobalGroupAggregate or LocalGroupAggregate node exists in the final topology.

-   Enable PartialFinal to resolve data hotspot issues when you use the COUNT DISTINCT function

    In normal cases, you need to add a layer that scatters data by distinct key when you use the COUNT DISTINCT function. This way, you can divide the aggregation process into two phases to resolve data hotspot issues. Realtime Compute for Apache Flink now provides the PartialFinal policy to automatically scatter data and divide the aggregation process. The following figure shows the difference between LocalGlobal and PartialFinal.

    ![PartialFinal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2716766261/p262015.png)

    The LocalGlobal policy improves the performance of common aggregate functions, such as SUM, COUNT, MAX, MIN, and AVG. However, the LocalGlobal policy is less effective for improving the performance of the COUNT DISTINCT function. This is because local aggregation cannot remove duplicate distinct keys. As a result, a large amount of data is stacked up in the global aggregation phase.

    -   Scenarios

        The PartialFinal policy is suitable for scenarios in which the aggregation performance cannot meet your requirements when you use the COUNT DISTINCT function.

        **Note:**

        -   You cannot enable PartialFinal in the Flink SQL code that contains user-defined aggregate functions \(UDAFs\).
        -   To prevent resources from being wasted, we recommend that you enable PartialFinal only when the amount of data is large. PartialFinal automatically scatters data and divides the aggregation process into two phases. This causes additional network shuffling.
    -   Method to enable PartialFinal

        PartialFinal is disabled by default. To enable this policy, you must enter the following code in the **Additional Configuration** section of the **Advanced** tab.

        ```
        table.optimizer.distinct-agg.split.enabled: true. 
        ```

    -   Verification

        Check whether one-layer aggregation changes to two-layer aggregation in the final topology.

-   AGG WITH CASE WHEN changed to AGG WITH FILTER to improve system performance in scenarios in which the COUNT DISTINCT function is used when a large amount of data exists

    Statistical jobs record unique visitors \(UVs\) in different dimensions, such as UVs of all the channels, UVs of mobile terminals, and UVs of PCs. We recommend that you use the standard AGG WITH FILTER syntax instead of the AGG WITH CASE WHEN syntax to implement multidimensional statistical analysis. The reason is that the SQL optimizer of Realtime Compute for Apache Flink can analyze the filter parameter. This way, Realtime Compute for Apache Flink can execute the COUNT DISTINCT function on the same field in different filter conditions by sharing the state data. This reduces the read and write operations on the state data. In the performance test, the AGG WITH FILTER syntax outperforms the AGG WITH CASE WHEN syntax. This is because the job performance for the AGG WITH FILTER syntax doubles that for the AGG WITH CASE WHEN syntax.

    -   Scenarios

        If you use AGG WITH FILTER instead of AGG WITH CASE WHEN when you use the COUNT DISTINCT function to calculate the results under different conditions on the same field, job performance improves significantly.

    -   Original statement

        ```
        COUNT(distinct visitor_id) as UV1 , COUNT(distinct case when is_wireless='y' then visitor_id else null end) as UV2
        ```

    -   Optimized statement

        ```
        COUNT(distinct visitor_id) as UV1 , COUNT(distinct visitor_id) filter (where is_wireless='y') as UV2
        ```


## Recommended Flink SQL practices

-   [TopN practices](#section_b0v_rga_gqc)
-   [Efficient deduplication](#section_qfl_p2j_yak)
-   [Efficient built-in functions](#section_m66_ru0_adj)

## TopN practices

-   TopN algorithms

    If the input streams of TopN are static streams, such as source, only the TopN algorithm AppendRank is used. If the input streams of TopN are dynamic streams such as streams processed by the aggregate or join function, three TopN algorithms are used in descending order of job performance: UpdateFastRank, UnaryUpdateRank, and RetractRank. The name of the algorithm in use is included in the name of a node in the topology.

    -   UpdateFastRank is the optimal algorithm.

        If you want to use this algorithm, make sure that the following conditions are met:

        -   The input streams must contain the primary key information. For example, the ORDER BY AVG clause is run based on the primary key.
        -   The values of the fields or functions such as ORDER BY COUNT, COUNT\_DISTINCT, or SUM \(positive value\) DESC in the ORDER BY clause are monotonically updated in the opposite order of sorting.

            If you use ORDER BY SUM DESC, you must specify a condition for filtering positive SUM values. This ensures that the total\_fee value is positive.

            ```
            insert
              into print_test
            SELECT
              cate_id,
              seller_id,
              stat_date,
              pay_ord_amt -- The rownum field is not included in the output data. This reduces the amount of output data that will be written to the result table. 
            FROM (
                SELECT
                  *,
                  ROW_NUMBER () OVER (
                    PARTITION BY cate_id,
                    stat_date -- Make sure that the stat_date field is included. Otherwise, the data may be disordered when the state data expires. 
                    ORDER
                      BY pay_ord_amt DESC
                  ) as rownum -- Sort data by the sum of the input data. 
                FROM (
                    SELECT
                      cate_id,
                      seller_id,
                      stat_date,
                      -- Note: The result of the SUM function is monotonically increasing because the values returned by the SUM function are positive. Therefore, TopN can use optimized algorithms to obtain top 100 data records. 
                      sum (total_fee) filter (
                        where
                          total_fee >= 0
                      ) as pay_ord_amt
                    FROM
                      random_test
                    WHERE
                      total_fee >= 0
                    GROUP
                      BY cate_name,
                      seller_id,
                      stat_date
                  ) a
                WHERE
                  rownum <= 100
              );                           
            ```

    -   UnaryUpdateRank is second only to UpdateFastRank in terms of performance. You can use this algorithm only when the input streams contain the primary key information.
    -   RetractRank ranks last in terms of performance. We recommend that you do not use this algorithm in the production environment. Check input streams. If input streams contain the primary key information, use UnaryUpdateRank or UpdateFastRank to optimize the job performance.
-   TopN optimization methods
    -   No-ranking optimization

        Do not include rownum in the output of TopN. We recommend that you sort the results when they are finally displayed in the frontend. This significantly reduces the amount of data that is to be written to the result table. For more information about no-ranking optimization methods, see [Top-N](https://ci.apache.org/projects/flink/flink-docs-master/docs/dev/table/sql/queries/#top-n).

    -   Increase the cache size of TopN

        TopN provides a state cache to improve the efficiency of accessing the state data. The following formula is used to calculate the cache hit ratio of TopN:

        ```
        cache_hit = cache_size*parallelism/top_n/partition_key_num
        ```

        For example, Top100 is used, the cache contains 10,000 records, and the parallelism is 50. If the number of keys for the PARTITION BY function is 100,000, the cache hit ratio is 5%. This ratio is calculated by using the formula: 10000 × 50/100/100,000 = 5%. The cache hit ratio is low, which indicates that a large number of requests access the disk state data. This significantly compromises the performance. Therefore, if the number of keys for the PARTITION BY function is large, you may increase the cache size and the heap memory of TopN.

        ```
        blink.topn.cache.size: 200000
        ```

        In this example, if you increase the cache size of TopN from the default value 10,000 to 200,000, the cache hit ratio may reach 100%. This cache hit ratio is calculated by using the following formula: 200,000 × 50/100/100000 = 100%.

    -   Include a time field in the PARTITION BY function

        For example, add the Day field to the ranking each day. Otherwise, the TopN results are disordered due to the time-to-live \(TTL\) of state.


## Efficient deduplication

Input streams of Realtime Compute for Apache Flink may contain duplicate data, which makes efficient deduplication a frequent demand. Realtime Compute for Apache Flink offers two policies to remove duplicates: Deduplicate Keep FirstRow and Deduplicate Keep LastRow.

-   Syntax

    Flink SQL does not provide the syntax to remove duplicates. To reserve the record in the first or last row of duplicate rows under the specified primary key and discard the other duplicates, you must use the SQL ROW\_NUMBER\(\) function that contains the OVER clause. Deduplication is a special TopN function.

    ```
    SELECT *
    FROM (
       SELECT *,
        ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
         ORDER BY timeAttributeCol [asc|desc]) AS rownum
       FROM table_name)
    WHERE rownum = 1
    ```

    |Parameter|Description|
    |---------|-----------|
    |ROW\_NUMBER\(\)|Calculates the row number. It is a window function that contains an OVER clause. The value starts from 1.|
    |PARTITION BY col1\[, col2..\]|Optional. Specifies partition columns for storing primary keys of duplicates.|
    |ORDER BY timeAttributeCol \[asc\|desc\]\)|Specifies the column by which you want to sort data. You must specify a time attribute, which can be proctime or rowtime. You can sort rows in ascending order or descending order based on the time attribute. For the ascending order, the record in the first row of duplicate rows is reserved. For the descending order, the record in the last row of duplicate rows is reserved.|
    |rownum|Specifies the number of rows. You can specify `rownum = 1` or `rownum <= 1`.|

    The preceding syntax shows that deduplication involves two-level queries:

    1.  Use the `ROW_NUMBER()` window function to sort data based on the specified time attribute and use rankings to mark the data.
        -   If the time attribute is proctime, Realtime Compute for Apache Flink removes duplicates based on the time when the records are processed. In this case, the ranking result may vary each time.
        -   If the time attribute is rowtime, Realtime Compute for Apache Flink removes duplicates based on the time when the records are written to Realtime Compute for Apache Flink. In this case, the ranking result remains the same each time.
    2.  Reserve only the record in the first row under the specified primary key and remove the other duplicates.

        You can sort records in ascending or descending order based on the time attribute.

        -   Deduplicate Keep FirstRow: Realtime Compute for Apache Flink sorts records in rows in ascending order based on the time attribute and reserves the record in the first row of duplicate rows under the specified primary key.
        -   Deduplicate Keep LastRow: Realtime Compute for Apache Flink sorts records in rows in descending order based on the time attribute and reserves the record in the first row of duplicate rows under the specified primary key.
-   Deduplicate Keep FirstRow

    If you select the Deduplicate Keep FirstRow policy, Realtime Compute for Apache Flink reserves the record in the first row of duplicate rows under the specified primary key and discards the other duplicates. In this case, the state data stores only the primary key information, and the efficiency of accessing the state data is significantly increased. The following example is used to explain the policy.

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    The preceding code removes duplicates from table T based on the b field, and reserves the record in the first row of duplicate rows under the specified primary key based on the system time. In this example, the proctime attribute indicates the system time when the records are processed in Realtime Compute for Apache Flink. Realtime Compute for Apache Flink sorts records in table T based on this attribute. To remove duplicates based on the system time, you can also call the PROCTIME\(\) function instead of declaring the proctime attribute.

-   Deduplicate Keep LastRow

    If you select the Deduplicate Keep LastRow policy, Realtime Compute for Apache Flink reserves the record in the last row of duplicate rows under the specified primary key and discards the other duplicates. This policy slightly outperforms the LAST\_VALUE function in terms of performance. The following example is used to explain the policy.

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY rowtime DESC) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    The preceding code removes duplicates from table T based on the b and d fields, and reserves the record in the last row under the specified primary key based on the time when the records are written to Realtime Compute for Apache Flink. In this example, the rowtime attribute indicates the event time when the records are written to Realtime Compute for Apache Flink. Realtime Compute for Apache Flink sorts records in table T based on this attribute.


## Efficient built-in functions

If you use built-in functions, take note of the following rules:

-   Use built-in functions to replace user-defined extensions \(UDXs\)

    Built-in functions of Realtime Compute for Apache Flink are under continual optimization. We recommend that you replace UDXs with built-in functions if possible. Realtime Compute for Apache Flink performs the following operations to optimize built-in functions:

    -   Improves the serialization and deserialization efficiency.
    -   Allows operations in the byte level.
-   Use single-character delimiters in the KEYVALUE function

    The signature of the KEYVALUE function is `KEYVALUE(content, keyValueSplit, keySplit, keyName)`. When keyValueSplit and KeySplit are single-character delimiters, such as colons \(:\) or commas \(,\), Realtime Compute for Apache Flink uses an optimized algorithm. Realtime Compute for Apache Flink searches for the required KeyName values in the binary data and does not segment the entire content. The job performance increases by about 30%.

-   Take note of the following points when you use the LIKE operator:
    -   To match records that start with the specified content, use `LIKE 'xxx%'`.
    -   To match records that end with the specified content, use `LIKE '%xxx'`.
    -   To match records that contain the specified content, use `LIKE '%xxx%'`.
    -   To match records that are the same as the specified content, use `LIKE 'xxx'`, which is equal to `str = 'xxx'`.
    -   To match an underscore \(\_\), use `LIKE '%seller/_id%' ESCAPE '/'`. The underscore \(\_\) is escaped because it is a single-character wildcard in SQL and can match all characters. If you use `LIKE '%seller_id%`, a large number of results are returned, such as `seller_id`, `iseller#id`, `sellerxid`, and `seller1id`. This may cause unexpected results.
-   Avoid using regular expressions

    Data processing by using regular expressions is time-consuming, which may cause performance overhead hundred times higher than the addition, subtraction, multiplication, or division operation. In addition, regular expressions may enter an infinite loop in some extreme cases, which results in job blocking. For more information, see [Regex execution is too slow](https://stackoverflow.com/questions/4500507/regex-execution-is-too-slow-in-java). Therefore, we recommend that you use the LIKE operator. For more information about some common regular expressions, click the following links:

    -   [REGEXP](/intl.en-US/Fully Managed Flink/Flink SQL Reference/Built-in functions/Scalar functions/REGEXP.md)
    -   [REGEXP\_REPLACE](/intl.en-US/Fully Managed Flink/Flink SQL Reference/Built-in functions/Scalar functions/REGEXP_REPLACE.md)

