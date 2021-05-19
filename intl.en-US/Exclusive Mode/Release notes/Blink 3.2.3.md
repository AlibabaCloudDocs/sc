# Blink 3.2.3

This topic lists the improvements and bug fixes in Blink 3.2.3 for Realtime Compute. Blink 3.2.3 is released to enhance your development experience.

## Improvements

-   Resolves the following issue: In the Realtime Compute console, the configuration details in the **Vertex Topology** section of the **Administration** page are inconsistent with those on the **Configuration** tab of the **Development** page.
-   Resolves the following issue of Blink 3.2.1: The task ID fails to be displayed on the **Curve Charts** page.
-   Resolves the issue: Garbled text exists in the logs on the TaskManager tab of the **Administration** page.
-   Resolves the following issue of Blink 3.2.1: The garbage collection log overwrites the debugging result. Before a job file is published, data debugging is performed.
-   Resolves the following issue: The automatic scaling feature for exclusive clusters can be enabled only by adding job parameters.
-   Resolves the following issue: If LEFT JOIN statements are executed, INNER JOIN statements are displayed in the **Vertex Topology** section on the **Administration** page.
-   Resolves the following issue: Users cannot locate the specific row where errors occur in the code editor.

## Bug fixes

-   Fixes the following bug: The REGEXP\_EXTRACT function does not return null if any argument is null or regular expression is invalid.
-   Fixes the following bug: Backward slashes \(\\\) of regular expressions in the code are compiled as semicolons \(;\).
-   Fixes the following bug: The debugging result that is printed to the taskmanager.out file is inaccurate. Before a job file is published, data debugging is performed.
-   Fixes the following bug: No errors are reported when the LEFT JOIN operation is performed on a dimension table that is not declared as the dimension table.
-   Fixes the following bug: Errors occur when the data of the time data type is written to the AnalyticDB for MySQL result table.
-   Fixes the following bug in Blink 3.2.1: If a JOIN operation is performed on a MaxCompute dimension table and the ON clause includes fields of the timestamp data type, job running errors occur.
-   Fixes the following bug in Blink 3.2.1 and 3.2.2: Job running errors occur when the `minibatch` parameter is used.
-   Fixes the following bug: The complex event processing \(CEP\) syntax passed the check on the Development page, but job running errors occur.
-   Fixes the following bug: The `maxRetryTimes` parameter in the ApsaraDB for Hbase result table fails to take effect.
-   Fixes the following bug: The group ID is not displayed in Alibaba Cloud Message Queue for Apache Kafka when Blink reads data from Alibaba Cloud Message Queue for Apache Kafka source tables.
-   Fixes the following bug: Running errors occur if only one record of the varbinary data type is written to the message queue result table.

