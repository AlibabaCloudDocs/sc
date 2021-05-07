# Blink 3.7.9

This topic describes the major features and bug fixes in Blink 3.7.9.

## Major features

Partitioned join is supported for dimension tables.

**Note:**

-   If the upstream data is Update Stream, the unique key of the upstream must contain the shuffle key when you perform a common partitioned join operation. This ensures the correctness of semantics.
-   If you enable partitioned join for dimension tables, hot spot issues may occur. Bucket partitioned join is introduced to address this issue. However, you cannot perform the bucket partitioned join operation if the upstream data is Update Stream. During the bucket partitioned join operation, the source table calculates and determines the concurrency for each data record and spreads each data record to a bucket in the downstream. Meanwhile, the dimension table caches each data record in the bucket to scatter hot spot data.

## Major bug fixes

Fixes the bug that the checkpoint file is deleted by mistake because FileSegmentStateHandle is not registered with GeminiKeyedStateHandle.

