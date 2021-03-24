# EMIT语句

EMIT语句可以使QUERY根据不同场景，定义不同的输出策略，从而达到控制延迟或提高数据准确性的效果。

**说明：** EMIT语句仅支持实时计算2.0以上版本。

## 使用限制

-   EMIT策略只支持TUMBLE和HOP窗口，暂不支持SESSION窗口。
-   如果一个Job有多个输出，则多个输出的EMIT需要定义成相同策略，后续会支持不同策略。
-   EMIT语法还不能用来配置minibatch的allowLateness，后续计划使用EMIT策略来声明allowLateness。

## 什么是EMIT策略

EMIT策略是指在Flink SQL中，QUERY根据不同场景选择不同的输出策略（例如最大延迟时长）。传统的ANSI SQL语法不支持该类输出策略。例如，1小时的时间窗口，窗口触发之前希望每分钟都能看到最新的结果，窗口触发之后希望不丢失迟到一天内的数据。如果1小时窗口内的统计结果无变化，则不更新输出结果；如果1小时窗口内的统计结果有变化，则更新输出结果。

针对这类场景，实时计算抽象出EMIT语法，并扩展到SQL语法。以下为不同场景下EMIT策略的示例：

-   窗口结束之前，按1分钟延迟输出，窗口结束之后无延迟输出。

    ```
    EMIT 
      WITH DELAY '1'MINUTE BEFORE WATERMARK,
      WITHOUT DELAY AFTER WATERMARK
    ```

-   窗口结束之前不输出，窗口结束之后无延迟输出。

    ```
    EMIT WITHOUT DELAY AFTER WATERMARK
    ```

-   全局都按1分钟的延迟输出（您可以启用minibatch参数来增加延迟）。

    ```
    EMIT WITH DELAY '1'MINUTE
    ```

-   窗口结束之前按1分钟延迟输出。

    ```
    EMIT WITH DELAY '1'MINUTE BEFORE WATERMARK 
    ```


## EMIT语法的用途

EMIT语法能够实现以下两种功能：

-   控制延迟：针对窗口，设置窗口触发之前的EMIT输出频率，减少结果输出延迟。
-   提高数据精确性：不丢弃窗口触发之后的迟到的数据，修正输出结果。

**说明：** 选择EMIT策略时，请权衡业务复杂程度和资源消耗情况。越低的输出延迟、越高的数据精确性，需要提供越高的计算开销。

## EMIT语法

EMIT语法定义在INSERT INTO输出语句中，定义输出结果的策略。当INSERT INTO中未配置EMIT语法时，保持原有默认行为，即只在WATERMARK触发时，输出一个窗口结果。

**说明：** EMIT语句只能置于INSERT INTO语句中的所有QUERY语句之后，不能置于VIEW语句中。

```
INSERT INTO tableName
<Query>
EMIT strategy [, strategy]*

strategy ::= {WITH DELAY timeInterval | WITHOUT DELAY} 
                [BEFORE WATERMARK |AFTER WATERMARK]

timeInterval ::='string' timeUnit            
```

|参数|说明|
|--|--|
|`WITH DELAY`|可延迟输出，即按指定时长间隔输出。|
|`WITHOUT DELAY`|不可以延迟输出，即每来一条数据就输出。|
|`BEFORE WATERMARK`|窗口结束之前的策略配置，即WATERMARK触发之前。|
|`AFTER WATERMARK`|窗口结束之后的策略配置，即WATERMARK触发之后。 **说明：** 如果配置了AFTER WATERMARK策略，需要使用明文方式声明`blink.state.ttl.ms`，标识最大延迟时长。 |

其中`strategy`的配置方式包括以下几种：

-   配置为一个BEFORE。
-   配置为一个AFTER。
-   配置为一个BEFORE和一个AFTER。

**说明：** `strategy`不支持同时配置为多个BEFORE或者多个AFTER。

## 生命周期

AFTER策略允许接收迟到的数据，窗口的状态（State）允许保留一定时长，等待迟到的数据。这段保留的时长称为生命周期TTL。运用AFTER策略后，通过明文声明`blink.state.ttl.ms`参数，您可以设置状态允许的生命周期。例如，`blink.state.ttl.ms=3600000`表示状态允许保留超时时长为1小时内的数据，超时时长大于1小时的数据不被录入状态。

## EMIT语法示例

窗口区间为1小时的滚动窗口`tumble_window`的语法示例如下。

```
CREATE VIEW tumble_window AS
SELECT 
  `id`,
  TUMBLE_START(rowtime, INTERVAL '1' HOUR) as start_time,
  COUNT(*) as cnt
FROM source
GROUP BY `id`, TUMBLE(rowtime, INTERVAL '1' HOUR);                    
```

默认`tumble_window`的输出需要等到1小时结束才能显示。如果您需要尽早看到窗口的结果（即使是不完整的结果），例如每分钟看到最新的窗口结果，可以添加如下语句。

```
INSERT INTO result
SELECT * FROM tumble_window
EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK; --窗口结束之前，每隔1分钟输出一次更新结果。                    
```

默认`tumble_window`会忽略并丢弃窗口结束后到达的数据，如果您需要将窗口结束后1天到达的数据统计进入结果，并且需要每接收1条数据后立刻更新结果，可以添加如下语句。

```
INSERT INTO result
SELECT * FROM tumble_window
EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK, 
     WITHOUT DELAY AFTER WATERMARK;  --窗口结束后，每收到一条数据输出一次更新结果。
```

此外，您还需要在**作业参数**中配置blink.state.ttl.ms = 86400000（增加1天状态生命周期）。

## DELAY概念

EMIT策略中的`DELAY`指的是用户可接受的数据延迟时长，该延迟是指从用户的数据进入实时计算，到看到结果数据（从实时计算系统输出）的时间（Event Time或Processing Time）。延迟的计算基于系统时间。动态表（流式数据在实时计算内部的存储）中的数据发生变化的时间和结果表（实时计算外部的存储）中显示新记录的时间的间隔，称为延迟。

假设，实时计算系统的处理耗时是0，则在流式数据积攒和Window等待窗口数据的过程可能会导致延迟。如果您指定了最多延迟30秒，则30秒可用于流式数据的积攒。如果Query是1小时的窗口，则最多延迟30秒的含义是每隔30秒更新结果数据。

-   配置`EMIT WITH DELAY '1' MINUTE`

    对于Group By聚合，系统会在1分钟内积攒流式数据。如果有Window且Window的Size大于1分钟，Window就每隔1分钟更新一次结果数据。如果Window的Size小于1分钟，因为窗口依靠Watermark的输出就能保证Latency SLA，所以系统就会忽略这个配置。

-   配置`EMIT WITHOUT DELAY`

    对于Group By聚合，不会启用minibatch参数来增加延迟，每来一条数据都会触发计算和输出。对于Window函数，也是每来一条数据都触发计算和输出。


