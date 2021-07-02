---
keyword: upstream and downstream storage
---

# Upstream and downstream storage

This topic describes the upstream and downstream storage supported by Flink.

-   Upstream and downstream SQL storage
    -   Source tables
        -   [Create a Datagen source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a Datagen source table.md)
        -   [Create a Log Service source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a Log Service source table.md)
        -   [Create a Message Queue for Apache Kafka source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a Message Queue for Apache Kafka source table.md)
        -   [Create a full MaxCompute source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a full MaxCompute source table.md)
        -   [Create a Message Queue for Apache RocketMQ source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a Message Queue for Apache RocketMQ source table.md)
        -   [Create a Hologres source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a Hologres source table.md)
        -   [Create a full Elasticsearch source table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a full Elasticsearch source table.md)
        -   [Create a PostgreSQL CDC source table \(public preview\)](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a PostgreSQL CDC source table (public preview).md)
        -   [Create a MySQL CDC source table \(public preview\)](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a source table/Create a MySQL CDC source table (public preview).md)
    -   Result tables
        -   [Create a Blackhole result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Blackhole result table.md)
        -   [Create a Log Service result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Log Service result table.md)
        -   [Create a Message Queue for Apache Kafka result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Message Queue for Apache Kafka result table.md)
        -   [Create a Tablestore result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Tablestore result table.md)
        -   [Create a MaxCompute result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a MaxCompute result table.md)
        -   [Create a Message Queue for Apache RocketMQ result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Message Queue for Apache RocketMQ result table.md)
        -   [Create an ApsaraDB for HBase result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create an ApsaraDB for HBase result table.md)
        -   [Create an ApsaraDB for Redis result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create an ApsaraDB for Redis result table.md)
        -   [Create a Hologres result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Hologres result table.md)
        -   [Create an ApsaraDB for MongoDB result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create an ApsaraDB for MongoDB result table.md)
        -   [Create an Elasticsearch result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create an Elasticsearch result table.md)
        -   [Create a Phoenix5 result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create a Phoenix5 result table.md)
        -   [Create an ApsaraDB RDS for MySQL result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create an ApsaraDB RDS for MySQL result table.md)
        -   [Create an AnalyticDB for MySQL V3.0 result table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a result table/Create an AnalyticDB for MySQL V3.0 result table.md)
    -   Dimension tables
        -   [Create an ApsaraDB for HBase dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create an ApsaraDB for HBase dimension table.md)
        -   [Create a Tablestore dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create a Tablestore dimension table.md)
        -   [Create a MaxCompute dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create a MaxCompute dimension table.md)
        -   [Create an ApsaraDB for Redis dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create an ApsaraDB for Redis dimension table.md)
        -   [Create an Elasticsearch dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create an Elasticsearch dimension table.md)
        -   [Create a Hologres dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create a Hologres dimension table.md)
        -   [Create an ApsaraDB RDS for MySQL dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create an ApsaraDB RDS for MySQL dimension table.md)
        -   [Create an AnalyticDB for MySQL V3.0 dimension table](/intl.en-US/Fully Managed Flink/Flink SQL Reference/DDL statements/Create a dimension table/Create an AnalyticDB for MySQL V3.0 dimension table.md)
-   Upstream and downstream DataStream storage
    -   For more information about upstream and downstream DataStream storage of the Apache Flink version, see [DataStream Connectors](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/connectors/).
    -   The following table lists the newly added upstream and downstream storage in fully managed Flink.

        |Connector version|VVR or Flink version|Connector type|
        |-----------------|--------------------|--------------|
        |1.11-vvr-2.1.3-SNAPSHOT|VVR 2.1.3 \(corresponds to Flink 1.11.3\)|        -   ververica-connector-common
        -   ververica-connector-random
        -   ververica-connector-datahub
        -   ververica-connector-odps
        -   ververica-connector-cloudhbase
        -   ververica-connector-phoenix
        -   ververica-connector-redis
        -   ververica-connector-elasticsearch
        -   ververica-connector-mongodb
        -   ververica-connector-hologres
        -   ververica-connector-jdbc
        -   ververica-connector-ads
        -   ververica-connector-adb-3.0
        -   ververica-connector-rds
        -   ververica-connector-ots
        -   ververica-connector-elasticsearch6
        -   ververica-connector-elasticsearch7
        -   ververica-connector-kafka
        -   ververica-connector-hadoop-shaded
        -   ververica-connector-cloudhbase-shaded
        -   ververica-connector-mysql
        -   ververica-connector-postgres
        -   ververica-connector-hive-2.3.6
        -   ververica-connector-hive-3.1.2
        -   ververica-connector-continuous-odps |
        |1.11-vvr-2.1.2-SNAPSHOT|VVR 2.1.2 \(corresponds to Flink 1.11.2\)|        -   ververica-connector-common
        -   ververica-connector-random
        -   ververica-connector-datahub
        -   ververica-connector-odps
        -   ververica-connector-cloudhbase
        -   ververica-connector-phoenix
        -   ververica-connector-redis
        -   ververica-connector-elasticsearch
        -   ververica-connector-mongodb
        -   ververica-connector-hologres
        -   ververica-connector-jdbc
        -   ververica-connector-ads
        -   ververica-connector-adb-3.0
        -   ververica-connector-rds
        -   ververica-connector-ots
        -   ververica-connector-elasticsearch6
        -   ververica-connector-elasticsearch7
        -   ververica-connector-kafka
        -   ververica-connector-hadoop-shaded
        -   ververica-connector-cloudhbase-shaded
        -   ververica-connector-mysql
        -   ververica-connector-postgres
        -   ververica-connector-hive-2.3.6
        -   ververica-connector-hive-3.1.2
        -   ververica-connector-continuous-odps |


