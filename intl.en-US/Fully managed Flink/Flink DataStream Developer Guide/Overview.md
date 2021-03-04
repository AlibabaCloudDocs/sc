---
keyword: Flink Datastream
---

# Overview

The Flink Serverless development console allows you to develop, submit, suspend, and stop Flink DataStream jobs.

**Note:**

-   If the upstream and downstream storage systems that Flink DataStream jobs access use a whitelist mechanism, you must configure whitelists. For more information, see [Configure a whitelist](/intl.en-US/Fully managed Flink/Preparations/Configure a whitelist.md).
-   Flink DataStream jobs supported by Realtime Compute for Apache Flink are developed based on the open source Flink version. For more information, see [Open source Flink version](https://github.com/apache/flink/tree/blink).

Flink DataStream allows you to call the underlying Flink Serverless API operations so that you can use Flink Serverless in a flexible manner.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6265749951/p65080.png)

Flink DataStream Developer Guide consists of the following topics:

-   Develop a job

    [Develop a job]() describes the limits and methods to develop DataStream API, Table API, and Python API jobs in Flink Serverless.

-   Submit a job

    [Submit a job]() describes how to submit Flink Serverless DataStream API, Table API, and Python API jobs to clusters for running.

-   Suspend and stop a job

    [Suspend or terminate a job](/intl.en-US/Fully managed Flink/Flink SQL Developer Guide/Suspend or terminate a job.md) describes how to suspend or stop a job in the RUNNING state in the Flink Serverless development console.


