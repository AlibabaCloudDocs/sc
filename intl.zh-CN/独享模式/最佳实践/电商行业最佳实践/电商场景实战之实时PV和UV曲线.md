# 电商场景实战之实时PV和UV曲线

本文以实时计算合作伙伴格格家的案例为例，为您介绍如何使用实时计算制作实时PV和UV曲线图。

## 背景

随着新零售的概念慢慢崛起，互联网电商行业竞争越来越激烈。实时数据信息对于电商行业尤为重要，例如实时地统计网页PV和UV的总量。

## 案例

-   业务架构图

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6079649951/p34588.png)

-   业务流程
    1.  通过数据总线（DataHub）提供的SDK，同步Binlog日志数据到DataHub。
    2.  阿里云实时计算订阅DataHub的数据进行实时计算。
    3.  插入实时数据到云数据库RDS。
    4.  通过阿里云DataV数据可视化或其他大屏展示结果数据。
-   准备工作

    |字段|数据类型|说明|
    |--|----|--|
    |account\_id|VARCHAR|用户ID|
    |client\_ip|VARCHAR|客户端IP|
    |client\_info|VACHAR|设备机型信息|
    |platform|VARHCAR|系统版本信息|
    |imei|VARCHAR|设备唯一标识|
    |version|BIGINT|版本号|
    |action|BIGINT|页面跳转描述|
    |gpm|VARCHAR|埋点链路|
    |c\_time|VARCHAR|请求时间|
    |target\_type|VARCHAR|目标类型|
    |target\_id|VARCHAR|目标ID|
    |udata|VARCHAR|扩展信息|
    |session\_id|VARHCAR|会话ID|
    |product\_id\_chain|VARHCAR|商品ID串|
    |cart\_product\_id\_chain|VARCHAR|加购商品ID|
    |tag|VARCHAR|特殊标记|
    |position|VARCHAR|位置信息|
    |network|VARCHAR|网络使用情况|
    |p\_dt|VARCHAR|时间分区|
    |p\_platform|VARCHAR|系统版本信息|

    |字段|数据类型|说明|
    |--|----|--|
    |summary\_date|BIGINT|统计日期|
    |summary\_min|VARCHAR|统计分钟|
    |pv|BIGINT|单击量|
    |uv|BIGINT|访客量**说明：** 一天内同个访客多次访问网站计为一个UV。 |
    |currenttime|TIMESTAMP|当前时间|

-   业务逻辑

    ```
    --数据的订单源表。
    CREATE TABLE source_ods_fact_log_track_action (
        account_id                        VARCHAR,--用户ID。
        client_ip                         VARCHAR,--客户端IP。
        client_info                       VARCHAR,--设备机型信息。
        platform                          VARCHAR,--系统版本信息。
        imei                              VARCHAR,--设备唯一标识。
        `version`                         VARCHAR,--版本号。
        `action`                          VARCHAR,--页面跳转描述。
        gpm                               VARCHAR,--埋点链路。
        c_time                            VARCHAR,--请求时间。
        target_type                       VARCHAR,--目标类型。
        target_id                         VARCHAR,--目标ID。
        udata                             VARCHAR,--扩展信息，JSON格式。
        session_id                        VARCHAR,--会话ID。
        product_id_chain                  VARCHAR,--商品ID串。
        cart_product_id_chain             VARCHAR,--加购商品ID。
        tag                               VARCHAR,--特殊标记。
        `position`                        VARCHAR,--位置信息。
        network                           VARCHAR,--网络使用情况。
        p_dt                              VARCHAR,--时间分区天。
        p_platform                        VARCHAR --系统版本信息。
    ) WITH (
        type='datahub',
        endPoint='yourEndpointURL',
        project='yourProjectName',
        topic='yourTopicName',
        accessId='yourAccessId',
        accessKey='yourAccessSecret',
        batchReadSize='1000'
    );
    
    CREATE TABLE result_cps_total_summary_pvuv_min (
        summary_date              bigint,--统计日期。
        summary_min               varchar,--统计分钟。
        pv                        bigint,--单击量。
        uv                        bigint,--一天内同个访客多次访问计算为一个UV。
        currenttime               timestamp,--当前时间。
        primary key (summary_date,summary_min)
    ) WITH (
        type= 'rds',
        url = 'yourRDSDatabaseURL',
        userName = 'yourDatabaseUserName',
        password = 'yourDatabasePassword',
        tableName = 'yourTableName'
    );
    
    CREATE VIEW result_cps_total_summary_pvuv_min_01 AS
    select 
    cast(p_dt as bigint) as summary_date --时间分区。
    ,count(client_ip) as pv --客户端的IP。
    ,count(distinct client_ip) as uv  --客户端去重。
    ,cast(max(c_time ) as TIMESTAMP)  as c_time  --请求的时间。
    from source_ods_fact_log_track_action
    group by p_dt;
    
    INSERT into  result_cps_total_summary_pvuv_min
    select 
    a.summary_date, --时间分区。
    cast(DATE_FORMAT(c_time,'HH:mm')  as varchar) as summary_min, --取出小时分钟级别的时间。
    a.pv,
    a.uv,
    CURRENT_TIMESTAMP  as currenttime  --当前时间。
    from result_cps_total_summary_pvuv_min_01 AS a
    ;                    
    ```

-   难点解析

    为了方便理解结构化代码和代码维护，我们推荐使用View（[数据视图概念](/intl.zh-CN/独享模式/Flink SQL参考/创建数据视图.md)）把业务逻辑拆分成两个模块。

    -   模块一

        ```
        CREATE VIEW result_cps_total_summary_pvuv_min_01 AS
        select 
        cast(p_dt as bigint) as summary_date, --时间分区。
        count(client_ip) as pv, --客户端的IP。
        count(distinct client_ip) as uv, --客户端去重。
        cast(max(c_time) as TIMESTAMP) as c_time  --请求的时间。
        from source_ods_fact_log_track_action
        group by p_dt;                                
        ```

        -   PV是客户访问网站后的单击次数，UV是去重的客户IP数。
        -   cast\(max\(c\_time\) as TIMESTAMP\)为最后请求时间。
        -   以p\_dt作为时间分区，单位为天，以max\(c\_time\)作为访问网站的截止时间，向数据库插入一条PV和UV。
        |p\_dt|pv|uv|`max(c_time)`|
        |-----|--|--|-------------|
        |2017-12-12|1000|100|`2017-12-12 9:00:00`|
        |2017-12-12|1500|120|`2017-12-12 9:01:00`|
        |2017-12-12|2200|200|`2017-12-12 9:02:00`|
        |2017-12-12|3300|320|`2017-12-12 9:03:00`|

    -   模块二

        ```
        INSERT into  result_cps_total_summary_pvuv_min
        select 
        a.summary_date, --时间分区，天单位为。
        cast(DATE_FORMAT(c_time,'HH:mm')  as varchar) as summary_min, --取出小时分钟级别的时间。
        a.pv,
        a.uv,
        CURRENT_TIMESTAMP  as currenttime  --当前时间。
        from result_cps_total_summary_pvuv_min_01 AS a;                         
        ```

        把模块一的数据精确到小时分钟级别取出后，根据数据得出PV和UV的增长曲线。

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6079649951/p34589.png)


## 示例及源代码

根据上文介绍的PV和UV曲线解决方案，阿里云为您创建了一个包含完整链路的DEMO示例，您可以参考DEMO示例，注册上下游存储，从而得出您的PV和UV曲线图。单击[示例代码](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/160581/cn_zh/1585880063555/UV%E6%9B%B2%E7%BA%BFDEMO.sql)下载完整示，使用示例时，上下游存储请注意以下两点：

-   DataHub作为源表。
-   RDS作为结果表。

