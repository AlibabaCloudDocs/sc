# print结果表

本文为您介绍print结果表的DDL定义，以及创建print结果表时使用的WITH参数。您可以通过print结果表测试流作业或者调试生产作业，验证数据结果是否符合预期。

**说明：**

-   如果您需要查看print结果表的结果数据，则需要将日志级别调至info，因为print sink数据输出为info日志。
-   应安全部门要求，系统对taskmanager.log日志展示数据条数进行了限制。如果您有排查脏数据或特定数据等需求，建议在Where条件中指定业务场景相关条件后，进行print操作，以避免因为数据条数限制无法排查。

## DDL定义

```
CREATE TABLE print_table (
  a INT,
  b varchar
) WITH (
  'connector'='print',
  'logger'='true'
);
```

## WITH参数

|参数|说明|是否必填|备注|
|--|--|----|--|
|connector|结果表类型|是|固定值为print。|
|logger|控制台是否显示数据结果|否|取值如下：-   false（默认值）：不显示。
-   true：显示。 |
|print-identifier|数据结果标识|否|在日志中通过数据结果标识检索信息。|
|sink.parallelism|结果表并行度|否|默认与上游并行度一致。|

## 常见问题

-   Q：如何在控制台查看print数据结果？
-   A：查看print数据结果的步骤，有以下两种方式：
    -   在Flink开发控制台查看：
        1.  在目标作业**Task Manager**页签，单击**Path, ID**。
        2.  在**logs**页签，查看print数据结果。
    -   跳转到Flink UI界面查看：
        1.  在目标作业**作业总览**页签，单击**Flink UI**。
        2.  单击**Task Managers**。
        3.  单击**Path, ID**。
        4.  在**logs**页签，查看print数据结果。

