# Real-time PV and UV curves in e-commerce scenarios

This topic provides a use case of Gegejia, a partner of Realtime Compute for Apache Flink, to describe how to use Realtime Compute for Apache Flink to create real-time page view \(PV\) and unique visitor \(UV\) curves.

## Background information

As the new retail industry rises, competition in the Internet e-commerce industry is becoming increasingly fierce. Real-time data is particularly important to the e-commerce industry, such as collecting statistics on the total PVs and UVs to a website.

## Example

-   Business architecture

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3112476851/p34588.png)

-   Workflow
    1.  The SDK provided by DataHub synchronizes binary logs to DataHub.
    2.  Realtime Compute for Apache Flink subscribes to data in DataHub for real-time computing.
    3.  Realtime Compute for Apache Flink writes real-time data to ApsaraDB RDS.
    4.  Alibaba Cloud DataV or other data visualization service presents the result data.
-   Preparations

    |Field|Data type|Description|
    |-----|---------|-----------|
    |account\_id|VARCHAR|The user ID.|
    |client\_ip|VARCHAR|The IP address of the client.|
    |client\_info|VACHAR|The model of the device.|
    |platform|VARHCAR|The operating system type of the device.|
    |imei|VARCHAR|The International Mobile Equipment Identity \(IMEI\) number of the device.|
    |version|BIGINT|The operating system version of the device.|
    |action|BIGINT|The page redirection description.|
    |gpm|VARCHAR|The tracking path.|
    |c\_time|VARCHAR|The time at which the request was made.|
    |target\_type|VARCHAR|The type of requested data.|
    |target\_id|VARCHAR|The ID of requested data.|
    |udata|VARCHAR|The extended information.|
    |session\_id|VARHCAR|The session ID|
    |product\_id\_chain|VARHCAR|The string of product IDs.|
    |cart\_product\_id\_chain|VARCHAR|The ID string of the products added to the cart.|
    |tag|VARCHAR|The special tag.|
    |position|VARCHAR|The location of the user.|
    |network|VARCHAR|The network type of the user.|
    |p\_dt|VARCHAR|The time-based partition, in days.|
    |p\_platform|VARCHAR|The partition system version.|

    |Field|Data type|Description|
    |-----|---------|-----------|
    |summary\_date|BIGINT|The date on which the statistics are collected.|
    |summary\_min|VARCHAR|The minute at which the statistics are collected.|
    |pv|BIGINT|The number of clicks on the specified website.|
    |uv|BIGINT|The number of visitors who click the specified website.**Note:** Only one UV is counted for multiple clicks by the same visitor within one day. |
    |currenttime|TIMESTAMP|The current system time.|

-   Business logic

    ```
    // Create an order source table.
    CREATE TABLE source_ods_fact_log_track_action (
        account_id                        VARCHAR, // The ID of the user.
        client_ip                         VARCHAR, // The IP address of the client.
        client_info                       VARCHAR, // The model of the device.
        platform                          VARCHAR, // The operating system type of the device.
        imei                              VARCHAR, // The IMEI number of the device.
        `version`                         VARCHAR, // The operating system version of the device.
        `action`                          VARCHAR, // The page redirection description.
        gpm                               VARCHAR, // The tracking path.
        c_time                            VARCHAR, // The time at which the request was made.
        target_type                       VARCHAR, // The type of the requested data.
        target_id                         VARCHAR, // The ID of the requested data.
        udata                             VARCHAR, // The extended information in the JSON format.
        session_id                        VARCHAR, // The ID of the session.
        product_id_chain                  VARCHAR, // The ID string of products.
        cart_product_id_chain             VARCHAR, // The ID string of the products added to the cart.
        tag                               VARCHAR, // The special tag.
        `position`                        VARCHAR, // The location of the user.
        network                           VARCHAR, // The network type of the user.
        p_dt                              VARCHAR, // The time-based partition, in days.
        p_platform                        VARCHAR // The partition system version.
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
        summary_date              bigint, // The date on which the statistics are collected.
        summary_min               varchar, // The minute at which the statistics are collected.
        pv                        bigint, // The number of clicks on the specified website.
        uv                        bigint, // The number of visitors who click the specified website. Only one UV is counted for multiple clicks by the same visitor within one day.
        currenttime               timestamp, // The current system time.
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
    cast(p_dt as bigint) as summary_date // The time-based partition, in days.
    ,count(client_ip) as pv // Count the number of PVs by the client IP address.
    ,count(distinct client_ip) as uv // Count the number of UVs by deduplicating client IP addresses.
    ,cast(max(c_time ) as TIMESTAMP)  as c_time // The time at which the request was made.
    from source_ods_fact_log_track_action
    group by p_dt;
    
    INSERT into  result_cps_total_summary_pvuv_min
    select 
    a.summary_date, // The time-based partition, in days.
    cast(DATE_FORMAT(c_time,'HH:mm')  as varchar) as summary_min, // Obtain the time string representing the hour and minute.
    a.pv,
    a.uv,
    CURRENT_TIMESTAMP  as currenttime // The current system time.
    from result_cps_total_summary_pvuv_min_01 AS a
    ;                    
    ```

-   Key points

    To help you understand structured code and facilitate code maintenance, we recommend that you use views to split the business logic into two modules. For more information about views, see [Create a data view](/intl.en-US/Exclusive Mode/Flink SQL/Create a data view.md).

    -   Module 1

        ```
        CREATE VIEW result_cps_total_summary_pvuv_min_01 AS
        select 
        cast(p_dt as bigint) as summary_date // The time-based partition, in days.
        ,count(client_ip) as pv // Count the number of PVs by client IP address.
        ,count(distinct client_ip) as uv // Count the number of UVs by deduplicating visitors.
        ,cast(max(c_time ) as TIMESTAMP)  as c_time // The time at which the request was made.
        from source_ods_fact_log_track_action
        group by p_dt;                                
        ```

        -   PV is the number of clicks after a customer visits the website, and UV is the number of unique visitors after customer IP addresses are duplicated.
        -   cast\(max\(c\_time\) as TIMESTAMP\) specifies the time at which the last request was made.
        -   p\_dt is used as the time-based partition, and the unit is day. max\(c\_time\) is used as the deadline for visiting a website, and a PV and UV are inserted into the database.
        |p\_dt|pv|uv|`max(c_time)`|
        |-----|--|--|-------------|
        |2017-12-12|1000|100|`2017-12-12 9:00:00`|
        |2017-12-12|1500|120|`2017-12-12 9:01:00`|
        |2017-12-12|2200|200|`2017-12-12 9:02:00`|
        |2017-12-12|3300|320|`2017-12-12 9:03:00`|

    -   Module 2

        ```
        INSERT into  result_cps_total_summary_pvuv_min
        select 
        a.summary_date, // The time-based partition, in days.
        cast(DATE_FORMAT(c_time,'HH:mm')  as varchar) as summary_min, // Obtain the time string that represents the hour and minute.
        a.pv,
        a.uv,
        CURRENT_TIMESTAMP  as currenttime // The current system time.
        from result_cps_total_summary_pvuv_min_01 AS a;                         
        ```

        Extract the data from module 1 by hour and minute and obtain the PV and UV growth curves,

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3112476851/p34589.png)


## Example and source code

Based on the PV and UV curve solution described in this topic, Alibaba Cloud creates a demo that includes a complete link for you. You can use this demo to register upstream and downstream storage resources and obtain your PV and UV curves. You can click [sample code](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/160581/cn_zh/1585880063555/UV%E6%9B%B2%E7%BA%BFDEMO.sql) to download the complete demo. Take note of the following two points when you use this demo to register upstream and downstream storage resources:

-   Use a DataHub table as the source table.
-   Create an ApsaraDB RDS result table.

