---
keyword: [Flink SQL优化, 热点, 数据倾斜]
---

# 高性能Flink SQL优化技巧

本文将从作业配置和Flink SQL优化两方面为您介绍如何提升Flink SQL作业性能。

## 作业配置优化推荐方案

-   资源优化技巧

    VVP中限制了JobManager和TaskManager的CPU的实际使用大小，配置了多少个CPU，最大就只能使用多少个CPU。因此在资源优化时，建议：

    -   作业并发大时：在作业的**高级配置**的**资源配置**中，增加JobManager的资源，提高CPU和内存的大小，例如：
        -   Job Manager CPUs设置为4。
        -   Job Manager Memory设置为8 GiB。
    -   作业拓扑较复杂时，在作业的**高级配置**的**资源配置**中，增加TaskManager的资源，提高CPU和内存的大小，例如：
        -   Task Manager CPUs设置为2。
        -   Task Manager Memory设置为4 GiB。
    -   不建议修改taskmanager.numberOfTaskSlots，保持默认值1。
-   提升吞吐和解决数据热点的推荐配置

    在作业**高级配置**的**更多Flink配置**里添加以下代码，详情请参见[Group Aggregate优化技巧](#section_211_m3y_k5i)。

    ```
    execution.checkpointing.interval: 180s
    state.backend: com.alibaba.flink.statebackend.GeminiStateBackendFactory
    state.backend.gemini.ttl.ms: 129600000
    table.exec.mini-batch.enabled: true
    table.exec.mini-batch.allow-latency: 5s
    table.optimizer.distinct-agg.split.enabled: true
    env.java.opts.taskmanager: -Xms4096m -XX:+UseParNewGC -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -verbose:gc -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkSweepGC -XX:+UseCMSCompactAtFullCollection -XX:CMSMaxAbortablePrecleanTime=1000 -XX:+CMSClassUnloadingEnabled -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:SurvivorRatio=5 -XX:ParallelGCThreads=4
    ```

    参数解释如下表所示。

    |参数|说明|
    |--|--|
    |execution.checkpointing.interval|checkpoint间隔时间，单位为毫秒。|
    |state.backend|state backend的配置。|
    |state.backend.gemini.ttl.ms|state数据生命周期，单位为毫秒。|
    |table.exec.mini-batch.enabled|是否开启minibatch。|
    |table.exec.mini-batch.allow-latency|批量输出数据的时间间隔。|
    |table.optimizer.distinct-agg.split.enabled|是否开启PartialFina优化，解决COUNT DISTINCT热点问题。|
    |env.java.opts.taskmanager|启动Java选项。**说明：** 当源表为SLS时，不能设置该参数，否则会造成性能瓶颈。 |


## Group Aggregate优化技巧

-   开启MiniBatch（提升吞吐）

    MiniBatch是缓存一定的数据后再触发处理，以减少对State的访问，从而提升吞吐并减少数据的输出量。

    MiniBatch主要基于事件消息来触发微批处理，事件消息会按您指定的时间间隔在源头插入。

    -   适用场景

        微批处理通过增加延迟换取高吞吐，如果您有超低延迟的要求，不建议开启微批处理。通常对于聚合场景，微批处理可以显著的提升系统性能，建议开启。

    -   开启方式

        MiniBatch默认关闭，您需要在作业**高级配置**中的**更多Flink配置**填写以下代码。

        ```
        table.exec.mini-batch.enabled: true
        table.exec.mini-batch.allow-latency: 5s
        ```

        参数解释如下表所示。

        |参数|说明|
        |--|--|
        |table.exec.mini-batch.enabled|是否开启mini-batch。|
        |table.exec.mini-batch.allow-latency|批量输出数据的时间间隔。|

-   开启LocalGlobal（解决常见数据热点问题）

    LocalGlobal本质上能够靠LocalAgg的聚合筛除部分倾斜数据，从而降低GlobalAgg的热点，提升性能。您可以结合下图理解LocalGlobal如何解决数据倾斜的问题。

    ![LocalGlobal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4473738161/p262011.png)

    LocalGlobal优化将原先的Aggregate分成Local和Global两阶段聚合，即MapReduce模型中的Combine和Reduce两阶段处理模式。第一阶段在上游节点本地攒一批数据进行聚合（localAgg），并输出这次微批的增量值（Accumulator）。第二阶段再将收到的Accumulator合并（Merge），得到最终的结果（GlobalAgg）。

    -   适用场景

        提升普通聚合（例如SUM、COUNT、MAX、MIN和AVG）的性能，以及这些场景下的数据热点问题。

    -   使用限制

        LocalGlobal是默认开启的，即blink.localAgg.enabled=true，但是有以下限制：

        -   在minibatch开启的前提下才能生效。
        -   需要使用AggregateFunction实现Merge。
    -   判断是否生效

        观察最终生成的拓扑图的节点名字中是否包含GlobalGroupAggregate或LocalGroupAggregate。

-   开启PartialFinal（解决COUNT DISTINCT热点问题）

    之前，为了解决COUNT DISTINCT的热点问题，通常需要手动改写为两层聚合（增加按Distinct Key取模的打散层）。目前，实时计算提供了COUNT DISTINCT自动打散，即PartialFinal优化，您无需自行改写为两层聚合。PartialFinal和LocalGlobal的原理对比参见下图。

    ![PartialFinal](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/5473738161/p262015.png)

    LocalGlobal优化针对普通聚合（例如SUM、COUNT、MAX、MIN和AVG）有较好的效果，对于COUNT DISTINCT收效不明显，因为COUNT DISTINCT在Local聚合时，对于DISTINCT KEY的去重率不高，导致在Global节点仍然存在热点问题。

    -   适用场景

        使用COUNT DISTINCT，但无法满足聚合节点性能要求。

        **说明：**

        -   不能在包含UDAF的Flink SQL中使用PartialFinal优化方法。
        -   数据量较少的情况，不建议使用PartialFinal优化方法，浪费资源。因为PartialFinal优化会自动打散成两层聚合，引入额外的网络Shuffle。
    -   开启方式

        默认不开启，您需要在作业**高级配置**中的**更多Flink配置**填写以下代码。

        ```
        table.optimizer.distinct-agg.split.enabled: true。
        ```

    -   判断是否生效

        观察最终生成的拓扑图，是否由原来一层的聚合变成了两层的聚合。

-   AGG WITH CASE WHEN改写为AGG WITH FILTER语法（提升大量COUNT DISTINCT场景性能）

    统计作业需要计算各种维度的UV，例如全网UV、来自手机客户端的UV、来自PC的UV等等。建议使用标准的AGG WITH FILTER语法来代替CASE WHEN实现多维度统计的功能。实时计算目前的SQL优化器能分析出Filter参数，从而同一个字段上计算不同条件下的COUNT DISTINCT能共享State，减少对State的读写操作。性能测试中，使用AGG WITH FILTER语法来代替CASE WHEN能够使性能提升1倍。

    -   适用场景

        对于同一个字段上计算不同条件下的COUNT DISTINCT结果的场景，性能提升很大。

    -   原始写法

        ```
        COUNT(distinct visitor_id) as UV1 , COUNT(distinct case when is_wireless='y' then visitor_id else null end) as UV2
        ```

    -   优化写法

        ```
        COUNT(distinct visitor_id) as UV1 , COUNT(distinct visitor_id) filter (where is_wireless='y') as UV2
        ```


## Flink SQL优化推荐方案

-   [TopN优化技巧](#section_b0v_rga_gqc)
-   [高效去重方案](#section_qfl_p2j_yak)
-   [高效的内置函数](#section_m66_ru0_adj)

## TopN优化技巧

-   TopN算法

    当TopN的输入是非更新流（例如Source），TopN只有1种算法AppendRank。当TopN的输入是更新流时（例如经过了AGG或JOIN计算），TopN有3种算法，性能从高到低分别是：UpdateFastRank 、 UnaryUpdateRank和RetractRank。算法名字会显示在拓扑图的节点名字上。

    -   UpdateFastRank ：最优算法。

        使用该算法需要具备2个条件：

        -   输入流有PK（Primary Key）信息，例如ORDER BY AVG。
        -   排序字段的更新是单调的，且单调方向与排序方向相反。例如，ORDER BY COUNT/COUNT\_DISTINCT/SUM（正数）DESC。

            如果您要获取到优化Plan，则您需要在使用ORDER BY SUM DESC时，添加SUM为正数的过滤条件，确保total\_fee为正数。

            ```
            insert
              into print_test
            SELECT
              cate_id,
              seller_id,
              stat_date,
              pay_ord_amt  --不输出rownum字段，能减小结果表的输出量。
            FROM (
                SELECT
                  *,
                  ROW_NUMBER () OVER (
                    PARTITION BY cate_id,
                    stat_date  --注意要有时间字段，否则State过期会导致数据错乱。
                    ORDER
                      BY pay_ord_amt DESC
                  ) as rownum  --根据上游sum结果排序。
                FROM (
                    SELECT
                      cate_id,
                      seller_id,
                      stat_date,
                      --重点。声明Sum的参数都是正数，所以Sum的结果是单调递增的，因此TopN能使用优化算法，只获取前100个数据。
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

    -   UnaryUpdateRank：性能仅次于UpdateFastRank的算法。使用该算法需要具备的条件是输入流中存在PK信息。
    -   RetractRank：普通算法，性能最差，不建议在生产环境使用该算法。请检查输入流是否存在PK信息，如果存在，则可使用UnaryUpdateRank或UpdateFastRank算法进行优化。
-   TopN优化方法
    -   无排名优化

        TopN的输出结果不需要显示rownum值，仅需在最终前端显示时进行1次排序，极大地减少输入结果表的数据量。无排名优化方法详情请参见[Top-N](https://ci.apache.org/projects/flink/flink-docs-master/docs/dev/table/sql/queries/#top-n)。

    -   增加TopN的Cache大小

        TopN为了提升性能有一个State Cache层，Cache层能提升对State的访问效率。TopN的Cache命中率的计算公式如下。

        ```
        cache_hit = cache_size*parallelism/top_n/partition_key_num
        ```

        例如，Top100配置缓存10000条，并发50，当您的PatitionBy的key维度较大时，例如10万级别时，Cache命中率只有10000\*50/100/100000=5%，命中率会很低，导致大量的请求都会击中State（磁盘），性能会大幅下降。因此当PartitionBy的Key维度特别大时，可以适当加大TopN的Cache Size，相对应的也建议适当加大TopN节点的Heap Memory。

        ```
        blink.topn.cache.size: 200000
        ```

        默认10000条，调整TopN cahce到200000，那么理论命中率能达到200000\*50/100/100000 = 100%。

    -   PartitionBy的字段中要有时间类字段

        例如每天的排名，要带上Day字段，否则TopN的最终结果会由于State TTL产生错乱。


## 高效去重方案

实时计算的源数据在部分场景中存在重复数据，去重成为了用户经常反馈的需求。实时计算有保留第一条（Deduplicate Keep FirstRow）和保留最后一条（Deduplicate Keep LastRow）2种去重方案。

-   语法

    由于SQL上没有直接支持去重的语法，还要灵活的保留第一条或保留最后一条。因此我们使用了SQL的ROW\_NUMBER OVER WINDOW功能来实现去重语法。去重本质上是一种特殊的TopN。

    ```
    SELECT *
    FROM (
       SELECT *,
        ROW_NUMBER() OVER ([PARTITION BY col1[, col2..]
         ORDER BY timeAttributeCol [asc|desc]) AS rownum
       FROM table_name)
    WHERE rownum = 1
    ```

    |参数|说明|
    |--|--|
    |ROW\_NUMBER\(\)|计算行号的OVER窗口函数。行号从1开始计算。|
    |PARTITION BY col1\[, col2..\]|可选。指定分区的列，即去重的KEYS。|
    |ORDER BY timeAttributeCol \[asc\|desc\]\)|指定排序的列，必须是一个时间属性的字段（即Proctime或Rowtime）。可以指定顺序（Keep FirstRow）或者倒序 （Keep LastRow）。|
    |rownum|仅支持`rownum=1`或`rownum<=1`。|

    如上语法所示，去重需要两层Query：

    1.  使用`ROW_NUMBER()` 窗口函数来对数据根据时间属性列进行排序并标上排名。
        -   当排序字段是Proctime列时，Flink就会按照系统时间去重，其每次运行的结果是不确定的。
        -   当排序字段是Rowtime列时，Flink就会按照业务时间去重，其每次运行的结果是确定的。
    2.  对排名进行过滤，只取第一条，达到了去重的目的。

        排序方向可以是按照时间列的顺序，也可以是倒序：

        -   Deduplicate Keep FirstRow：顺序并取第一条行数据。
        -   Deduplicate Keep LastRow：倒序并取第一条行数据。
-   Deduplicate Keep FirstRow

    保留首行的去重策略：保留KEY下第一条出现的数据，之后出现该KEY下的数据会被丢弃掉。因为STATE中只存储了KEY数据，所以性能较优，示例如下。

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b ORDER BY proctime) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    以上示例是将T表按照b字段进行去重，并按照系统时间保留第一条数据。proctime在这里是源表T中的一个具有Processing Time属性的字段。如果您按照系统时间去重，也可以将proctime字段简化proctime\(\)函数调用，可以省略proctime字段的声明。

-   Deduplicate Keep LastRow

    保留末行的去重策略：保留KEY下最后一条出现的数据。保留末行的去重策略性能略优于LAST\_VALUE函数，示例如下。

    ```
    SELECT *
    FROM (
      SELECT *,
        ROW_NUMBER() OVER (PARTITION BY b, d ORDER BY rowtime DESC) as rowNum
      FROM T
    )
    WHERE rowNum = 1
    ```

    以上示例是将T表按照b和d字段进行去重，并按照业务时间保留最后一条数据。rowtime在这里是源表T中的一个具有Event Time属性的字段。


## 高效的内置函数

在使用内置函数时，您需要注意以下几点：

-   使用内置函数替换自定义函数

    实时计算的内置函数在持续的优化当中，请尽量使用内置函数替换自定义函数。实时计算对内置函数主要进行了如下优化：

    -   优化数据序列化和反序列化的耗时。
    -   新增直接对字节单位进行操作的功能。
-   KEY VALUE函数使用单字符的分隔符

    KEY VALUE的签名：`KEYVALUE(content, keyValueSplit, keySplit, keyName)`，当keyValueSplit和KeySplit是单字符，例如，冒号（:）、逗号（,）时，系统会使用优化算法，在二进制数据上直接寻找所需的keyName值，而不会将整个content进行切分，性能约提升30%。

-   LIKE操作注意事项
    -   如果需要进行StartWith操作，使用`LIKE 'xxx%'`。
    -   如果需要进行EndWith操作，使用`LIKE '%xxx'`。
    -   如果需要进行Contains操作，使用`LIKE '%xxx%'`。
    -   如果需要进行Equals操作，使用`LIKE 'xxx'`，等价于`str = 'xxx'`。
    -   如果需要匹配下划线（\_），请注意要完成转义`LIKE '%seller/_id%' ESCAPE '/'`。下划线（\_）在SQL中属于单字符通配符，能匹配任何字符。如果声明为`LIKE '%seller_id%'`，则不单会匹配`seller_id`，还会匹配`seller#id`、`sellerxid`或`seller1id`等，导致结果错误。
-   慎用正则函数（REGEXP）

    正则表达式是非常耗时的操作，对比加减乘除通常有百倍的性能开销，而且正则表达式在某些极端情况下可能会进入无限循环，导致作业阻塞，具体情况请参见[Regex execution is too slow](https://stackoverflow.com/questions/4500507/regex-execution-is-too-slow-in-java)，因此建议使用LIKE。正则函数包括：

    -   [REGEXP](/intl.zh-CN/Flink全托管/Flink SQL参考/内置函数/标量函数/REGEXP.md)
    -   [REGEXP\_REPLACE](/intl.zh-CN/Flink全托管/Flink SQL参考/内置函数/标量函数/REGEXP_REPLACE.md)

