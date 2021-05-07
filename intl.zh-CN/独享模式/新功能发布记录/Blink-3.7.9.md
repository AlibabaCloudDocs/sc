# Blink-3.7.9

本文为您介绍Blink 3.7.9版本的重大功能变更和主要修复缺陷。

## 版本重大功能变更

增加维表Partitioned Join校验。

**说明：**

-   对于普通Patitioned Join，如果上游是更新流，为了保证语义的正确性，上游Unique Key必须包含Shuffle Key。
-   在维表开启Partitioned Join时，可能发生热点问题。因此，引入了Bucket Partitioned Join解决该问题。但是对于Bucket Partitioned Join，上游不能是更新流。源表计算每条数据应该去的并发，然后打散到下游的某个Bucket里，维表侧会在Bucket并发上都缓存该条维表数据，达到热点数据打散的效果。

## 主要缺陷修复

修复由于GeminiKeyedStateHandle没有注册FileSegmentStateHandle，导致Checkpoint文件被误删的缺陷。

