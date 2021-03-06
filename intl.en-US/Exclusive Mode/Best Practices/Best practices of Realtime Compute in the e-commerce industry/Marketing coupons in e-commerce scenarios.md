# Marketing coupons in e-commerce scenarios

This topic describes how to use Realtime Compute for Apache Flink to filter users who meet the conditions for issuing marketing coupons in a coupon-based marketing policy.

## Background information

A merchant uses a marketing policy of refund coupons at Double 11. After the consumption amount of a user reaches a specified value, the merchant issues a refund coupon with a specific amount to the user to promote more consumption. Realtime Compute for Apache Flink monitors the consumption amount of users in real time and filters users who meet the conditions for issuing refund coupons.

## Solution

-   SQL structure

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0012476851/p34616.png)

-   Source tables
    -   Create a source table to store orders generated by the e-commerce system.

        **Note:** The order data format is simplified to focus on the core logic. Only the attributes related to the use case are retained.

        ```
        CREATE TABLE dwd_tb_trd_pay_ri(
                biz_order_id    VARCHAR, -- 'Order ID' 
                auction_id      VARCHAR, -- 'Product ID'
                auction_title   VARCHAR, -- 'Product title' 
                buyer_id        VARCHAR, -- 'ID of the buyer'
                buyer_nick      VARCHAR, -- 'Nickname of the buyer'
                pay_time        VARCHAR, -- 'Payment time of the order'
                gmt_create      VARCHAR, -- 'Time at which the order was created'
                gmt_modified    VARCHAR, -- 'Time at which the order was modified'
                biz_type        VARCHAR, -- 'Transaction type'
                pay_status      VARCHAR, -- 'Payment status' 
                `attributes`    VARCHAR, -- 'Flag of the order'
                from_group      VARCHAR, -- 'Source of the order'
                div_idx_actual_total_fee   DOUBLE  --'Transaction amount'        
        ) WITH (
            type='datahub',
            endPoint='http://dh-cn-hangzhou.aliyun-inc.com',
            project='yourProjectName',-- 'Name of your project'
            topic='yourTopicName',--'Name of your topic'
            roleArn='yourRoleArn',-- 'Alibaba Cloud Resource Name (ARN) of your role'
            batchReadSize='500'
        );               
        ```

    -   Create a source table of returns.

        **Note:** The order data format is simplified to focus on the core logic. Only the attributes related to the use case are retained.

        ```
        CREATE TABLE dwd_tb_trd_rfd_ri(
                biz_order_id    VARCHAR, -- 'Order ID' 
                auction_id      VARCHAR, -- 'Product ID'
                auction_title   VARCHAR, -- 'Product title' 
                buyer_id        VARCHAR, -- 'ID of the buyer'
                buyer_nick      VARCHAR, -- 'Nickname of the buyer'
                pay_time        VARCHAR, -- 'Payment time of the order'
                gmt_create      VARCHAR, -- 'Time at which the order was created'
                gmt_modified    VARCHAR, -- 'Time at which the order was modified'
                biz_type        VARCHAR, -- 'Transaction type'
                pay_status      VARCHAR, -- 'Payment status' 
                `attributes`    VARCHAR, -- 'Flag of the order'
                from_group      VARCHAR, -- 'Source of the order' 
                refund_fee      DOUBLE   -- 'Refund amount'     
        ) WITH (
            type='datahub',
            endPoint='http://dh-cn-hangzhou.aliyun-inc.com',
            project='yourProjectName', --'Your project'
            topic='yourTopicName', --'Your topic'
            roleArn='yourRoleArn', --'ARN of your role'
            batchReadSize='500'
        );                   
        ```

-   Result tables

    [Execute the following statements to create an ApsaraDB RDS result table:](/intl.en-US/Exclusive Mode/Flink SQL/DDL statements/Create a result table/Create an ApsaraDB RDS result table.md)

    ```
    CREATE TABLE tddl_output(
        gmt_create   VARCHAR, --'Time at which the order was created'
        gmt_modified VARCHAR, --'Time at which the order was modified'
        buyer_id     BIGINT, --'ID of the buyer'
        cumulate_amount BIGINT, --'Transaction amount'
        effect_time BIGINT, --'Payment time of the order'
        primary key(buyer_id,effect_time)
    ) WITH ( 
            type= 'rds', 
            url = 'yourDatabaseURL',  --'URL of your database' 
            tableName = 'yourTableName', --'Name of your table'
            userName = 'yourUserName',  --'Your username' 
            password = 'yourDatabasePassword' --'Your password' 
        );      
    ```

-   Business logic

    Perform the UNION ALL operation to join the order table and table of returns to obtain information about all purchased items and collect the actual consumption amount and details of users.

    ```
    CREATE VIEW  dwd_tb_trd_and_rfd_pay_ri
    AS
    SELECT
    *
    FROM(
        (SELECT 
            `a`.biz_order_id biz_order_id, 
            `a`.auction_id auction_id, 
            `a`.auction_title auction_title, 
            `a`.buyer_id buyer_id, 
            `a`.buyer_nick buyer_nick, 
            `a`.pay_time pay_time, 
            `a`.gmt_create gmt_create, 
            `a`.gmt_modified gmt_modified, 
            `a`.biz_type biz_type, 
            `a`.pay_status pay_status, 
            `a`.`attributes` `attributes`, 
            `a`.from_group,        
            `a`.div_idx_actual_total_fee div_idx_actual_total_fee
        FROM 
            dwd_tb_trd_pay_ri `a` 
        )
        UNION ALL
        (
        SELECT 
            `b`.biz_order_id biz_order_id, 
            `b`.auction_id auction_id, 
            `b`.auction_title auction_title, 
            `b`.buyer_id buyer_id, 
            `b`.buyer_nick buyer_nick, 
            `b`.pay_time pay_time, 
            `b`.gmt_create gmt_create, 
            `b`.gmt_modified gmt_modified, 
            `b`.biz_type biz_type, 
            `b`.pay_status pay_status, 
            `b`.`attributes` `attributes`, 
            `b`.from_group,     
            `b`.refund_fee div_idx_actual_total_fee --The refund amount, which is a negative value.
        FROM 
            dwd_tb_trd_rfd_ri `b` 
        )
    );               
    ```

-   Deduplication

    A large amount of duplicate data such as product IDs and product names may exist in the order table and return table. Use the MIN function to obtain parameter values only in the first generated record of each order and filter out other data. Then, group data by order ID and payment status to obtain required product IDs and product names.

    ```
    
    CREATE VIEW filter_hxhb_dwd_tb_trd_and_rfd_pay_ri_distinct AS 
    SELECT 
       biz_order_id biz_order_id, 
       pay_status pay_status,
       MIN(auction_id) auction_id, 
       MIN(auction_title) auction_title, 
       MIN(buyer_id) buyer_id, 
       MIN(buyer_nick)  buyer_nick, 
       MIN(pay_time)  pay_time, 
       MIN(gmt_create)  gmt_create, 
       MIN(gmt_modified)  gmt_modified, 
       MIN(biz_type)  biz_type, 
       MIN(attributes)  attributes, 
       MIN(div_idx_actual_total_fee)  div_idx_actual_total_fee
    FROM 
        dwd_tb_trd_and_rfd_pay_ri
    GROUP BY biz_order_id ,pay_status;              
    ```

-   Data aggregation

```
CREATE VIEW ads_tb_trd_and_rfd_pay_ri AS
SELECT 
   MIN(gmt_create) gmt_create,  --'Time at which the order was created'
   MAX(gmt_modified) gmt_modified,  --'Time at which the order was last modified'
   CAST (buyer_id AS BIGINT) buyer_id,  --'ID of the buyer'
   CAST (date_format(pay_time , 'yyyy-MM-dd HH:mm:ss' , 'yyyyMMdd') AS BIGINT) as effect_time, --'Payment time'
   SUM(CAST(div_idx_actual_total_fee/100 AS BIGINT)) cumulate_amount  --'Transaction amount'
FROM 
   filter_hxhb_dwd_tb_trd_and_rfd_pay_ri_distinct 
GROUP BY 
   buyer_id,date_format(pay_time , 'yyyy-MM-dd HH:mm:ss' , 'yyyyMMdd');                 
```

    Q: Why are the MAX and MIN functions used?

    ```
     MIN(gmt_create) gmt_create, --'Time at which the order was created'
     MAX(gmt_modified) gmt_modified, --'Time at which the order was last modified'                  
    ```

    A: MIN\(gmt\_create\) obtains the time at which an order was created. MAX\(gmt\_modified\) obtains the time at which the order was last modified. You can use MAX and MIN to obtain the time values based on the business logic of orders.

    **Note:** If the time field is not of the BIGINT type, use the related built-in functions to convert the data type. For more information, see [Built-in functions](/intl.en-US/Exclusive Mode/Flink SQL/Built-in functions/Type conversion function/CAST.md).

-   Data insertion into ApsaraDB RDS

    Insert the statistical data into the ApsaraDB RDS result table. Then, use appropriate push software to issue coupons with the correct refund amount to users who meet the specified conditions.

    ```
    INSERT INTO tddl_output
    SELECT
        gmt_create,
        gmt_modified,
        buyer_id,
        cumulate_amount,
        effect_time
    from ads_tb_trd_and_rfd_pay_ri
    where cumulate_amount>0;               
    ```

    Q: Why is the "cumulate\_amount\>0" clause used?

    ```
    cumulate_amount>0      
    ```

    A: This clause filters out the amount of the returned products involved in the preceding UNION ALL operation.


## Summary

Q: How do I obtain the data I need from a large number of records about orders and return orders?

A: In actual transaction situations, a large number of order records and return records are generated. You can perform the UNION ALL operation to join two or more tables as one large table. Then, deduplicate and aggregate the records based on your specific business logic. Finally, write the actual transaction amounts of all orders of each user into the result table to prepare for issuing coupons.

## Demo example and source code

Based on the marketing refund solution described in this topic, Alibaba Cloud creates a demo that includes a complete process for you.

-   Use a DataHub table as the source table.
-   Create an ApsaraDB RDS result table.

You can refer to this demo to register upstream and downstream storage resources and develop your marketing refund solutions. Click the [demo code](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/114415/cn_zh/1555312580637/%E8%90%A5%E9%94%80%E7%BA%A2%E5%8C%85DEMO.sql) to download it.

