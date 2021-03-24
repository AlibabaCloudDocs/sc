# EMIT statements

You can execute EMIT statements to define different output policies for a query in different scenarios. This allows you to control delays and improves data accuracy.

**Note:** Only Realtime Compute for Apache Flink V2.0.0 and later support EMIT statements.

## Limits

-   Only tumbling and sliding windows support EMIT policies. Session windows do not support EMIT policies.
-   If a job has multiple outputs, you must define the same EMIT policy for the outputs. You will be able to define different EMIT policies for the outputs in the future.
-   The EMIT syntax cannot be used to set the allowLateness parameter for minibatch. You will be able to declare allowLateness in EMIT policies in the future.

## EMIT policies

An EMIT policy is an output policy for a query in a specific scenario of Flink SQL. For example, an output policy may specify the maximum delay for outputs. The traditional ANSI SQL syntax does not support the output policies of this type. Assume that you want to view the latest result every minute before a 1-hour window ends and do not want to lose the data that arrives within one day after the window ends. If the collected statistics do not change before the window ends, the output result is not updated. If the collected statics change before the window ends, the output result is updated.

Based on this scenario, Realtime Compute for Apache Flink introduces the EMIT syntax and extends SQL statements with the EMIT syntax. In the following example, EMIT policies are defined for different scenarios:

-   Before a window ends, results are generated with a 1-minute delay. After the window ends, results are generated without delays.

    ```
    EMIT 
      WITH DELAY '1'MINUTE BEFORE WATERMARK,
      WITHOUT DELAY AFTER WATERMARK
    ```

-   Before a window ends, no result is generated. After the window ends, results are generated without delays.

    ```
    EMIT WITHOUT DELAY AFTER WATERMARK
    ```

-   Results are generated with a 1-minute delay. You can set the minibatch parameter to increase the delay.

    ```
    EMIT WITH DELAY '1'MINUTE
    ```

-   Before the window ends, results are generated with a 1-minute delay.

    ```
    EMIT WITH DELAY '1'MINUTE BEFORE WATERMARK 
    ```


## Usage

The EMIT syntax offer the following features:

-   Controls delays. You can set the output frequency before the window ends to decrease the delay of displaying results.
-   Improves data accuracy. The system does not discard the data that arrives late. This ensures the accuracy of outputs.

**Note:** When you specify an EMIT policy, you must balance between business complexity and resource consumption. A lower output delay and a higher data accuracy require a higher computing overhead.

## Syntax

You can use the EMIT syntax in the INSERT INTO statement to define a policy for outputs. If you do not use the EMIT syntax in the INSERT INTO statement, the default setting takes effect. By default, a window generates a result only when the window ends. A watermark is triggered when the window ends.

**Note:** You can append an EMIT statement only to the end of the query statements in the INSERT INTO statement. You cannot include an EMIT statement in a VIEW statement.

```
INSERT INTO tableName
<Query>
EMIT strategy [, strategy]*

strategy ::= {WITH DELAY timeInterval | WITHOUT DELAY} 
                [BEFORE WATERMARK |AFTER WATERMARK]

timeInterval ::='string' timeUnit            
```

|Parameter|Description|
|---------|-----------|
|`WITH DELAY`|Specifies the maximum output delay. Results are generated at the specified interval.|
|`WITHOUT DELAY`|Specifies that no delay is allowed. A result is immediately generated after each data record arrives.|
|`BEFORE WATERMARK`|Specifies the policy that is used before a window ends. A watermark is triggered when a window ends.|
|`AFTER WATERMARK`|Specifies the policy that is used after a window ends. A watermark is triggered when a window ends. **Note:** If you configure the AFTER WATERMARK policy, you must explicitly declare the `blink.state.ttl.ms` parameter to specify the maximum delay. |

You can use the following `strategy` settings:

-   Set it to one BEFORE policy.
-   Set it to one AFTER policy.
-   Set it to one BEFORE policy and one AFTER policy.

**Note:** You cannot define two BEFORE policies or two AFTER policies at the same time for `strategy`.

## TTL

If the AFTER WATERMARK policy is configured, the information about the window state is retained for a specified period to wait for late data. The retention period is called time to live \(TTL\). After the AFTER policy is applied, you can explicitly declare the `blink.state.ttl.ms` parameter to set the TTL for the information about the window state. For example, `blink.state.ttl.ms = 3600000` means that the window can wait for late data for up to 1 hour. The data that arrives more than 1 hour late is discarded.

## Examples

A 1-hour tumbling window is used as an example. The following code block describes the `tumble_window` syntax:

```
CREATE VIEW tumble_window AS
SELECT 
  `id`,
  TUMBLE_START(rowtime, INTERVAL '1' HOUR) as start_time,
  COUNT(*) as cnt
FROM source
GROUP BY `id`, TUMBLE(rowtime, INTERVAL '1' HOUR);                    
```

By default, you must wait for 1 hour before you can obtain the `tumble_window` result. If you need to view the latest result of the window every minute even if the result is incomplete, you can execute the following statements:

```
INSERT INTO result
SELECT * FROM tumble_window
EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK; --Before the window ends, the updated result is generated at 1-minute intervals.                    
```

By default, `tumble_window` ignores and discards the data that arrives after the window ends. In some scenarios, you may want the outputs to include the data that arrives one day after the window ends. You may also want the results to be immediately updated after each data record is received. To meet these requirements, you can execute the following statements:

```
INSERT INTO result
SELECT * FROM tumble_window
EMIT WITH DELAY '1' MINUTE BEFORE WATERMARK, 
     WITHOUT DELAY AFTER WATERMARK; --After the window ends, updated results are immediately generated after a data record is received.
```

In addition, you must set blink.state.ttl.ms to 86400000 in the **job parameters**. This value indicates that the window can wait for late data for up to one day.

## Delay

In an EMIT policy, `DELAY` specifies the maximum allowed duration. The duration starts from the time when your data flows into Realtime Compute for Apache Flink and ends at the time when you obtain the result data. The end time is an event time or a processing time. A delay is calculated based on the system time. The delay is the interval between the time when data changes in a dynamic table and the time when a new data record is displayed in a result table. The dynamic table stores streaming data in Realtime Compute for Apache Flink. The result table is referenced by Realtime Compute for Apache Flink and is stored in an external data store.

If the processing time in Realtime Compute for Apache Flink is 0, a delay may occur when streaming data accumulates and when a window waits for data. If you specify a maximum delay of 30 seconds, streaming data can be accumulated during the 30 seconds. If a 1-hour window is specified in a query, a maximum delay of 30 seconds indicates that the output results are updated every 30 seconds.

-   Use the configuration `EMIT WITH DELAY '1' MINUTE` as an example.

    When you use the GROUP BY clause to aggregate data, the system accumulates streaming data within 1 minute. For a window whose size is greater than 1 minute, the window generates a result every 1 minute. If the size of the window is less than 1 minute, the system ignores this configuration. This is because watermarks can be used to meet the delay requirements in the service level agreement \(SLA\) for window outputs.

-   Use the configuration `EMIT WITHOUT DELAY` as an example.

    When you use the GROUP BY clause to aggregate data, the system does not use the minibatch parameter to increase the delay. Each data record is immediately processed after it is received. The processing result is also immediately generated. When you use window functions, each data record is immediately processed after it is received. The processing result is also immediately generated.


