# Overview

This topic describes the types and definitions of user-defined extensions \(UDXs\) in Realtime Compute for Apache Flink.

## UDX types

Realtime Compute for Apache Flink supports three types of UDXs, as described in the following table.

|UDX type|Description|
|--------|-----------|
|User-defined scalar function \(UDF\)|A UDF maps zero, one, or multiple scalar values to a new scalar value. The input and output of a UDF are mapped in a one-to-one relationship. Each time a UDF reads a row of data, it writes an output value. For more information, see [UDFs]().|
|User-defined aggregation function \(UDAF\)|A UDAF aggregates multiple values into a single value. The input and output of a UDAF are mapped in a many-to-one relationship. Multiple input records are aggregated to generate one output value. For more information, see [UDAFs]().|
|User-defined table-valued function \(UDTF\)|A UDTF takes zero, one, or multiple scalar values as input parameters. These parameters can be variable-length parameters. UDTFs are similar to UDFs but differ in that they can return any number of rows instead of a single value. Returned rows can consist of one or more columns. A function invocation can return data in multiple rows or columns. For more information, see [UDTFs]().|

