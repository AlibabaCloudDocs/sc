# Create a custom result table

This topic describes how to create a custom result table in Realtime Compute for Apache Flink. Custom result tables can meet different data output requirements.

**Note:**

-   This topic applies only to Blink 1.4.5 and later.
-   This topic applies only to Realtime Compute for Apache Flink in exclusive mode.

## Build a development environment

You can use one of the following methods to build a development environment for a custom result table:

-   Use the development environment provided in examples.

    To accelerate the development of your services, Realtime Compute for Apache Flink provides the following examples of custom result tables:

    -   [Realtime Compute for Apache Flink V3.0](https://github.com/RealtimeCompute/blink_customersink_3x)
    -   [Realtime Compute for Apache Flink V2.0](https://github.com/RealtimeCompute/blink_customersink_2x)
    -   [Realtime Compute for Apache Flink V1.0](https://github.com/RealtimeCompute/blink_customersink_1x)
    **Note:** These examples provide development environments for specific versions. You do not need to build another development environment.

-   Download a JAR package and build your own environment.

    **Note:** If the following dependencies are referenced in a Maven project, you must set the Scope parameter to `<scope>provided</scope>`.

    -   Realtime Compute for Apache Flink V3.0
        -   JAR packages that you need to download

            -   [blink-connector-custom-blink-3.2.1](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1559663345940/blink-connector-custom-blink-3.2.1.jar)
            -   [blink-connector-common-blink-3.2.1](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1565233225498/blink-connector-common-blink-3.2.1.jar)
            You must add the following information to the POM file to automatically download the `flink-table_2.11` JAR package.

            ```
            <profiles>
              <profile>
                 <id>allow-snapshots</id>
                    <activation><activeByDefault>true</activeByDefault></activation>
                 <repositories>
                   <repository>
                     <id>snapshots-repo</id>
                     <url>https://oss.sonatype.org/content/repositories/snapshots</url>
                     <releases><enabled>false</enabled></releases>
                     <snapshots><enabled>true</enabled></snapshots>
                   </repository>
                 </repositories>
               </profile>
            </profiles>
            ```

        -   Dependencies

            ```
             <dependencies>
              <dependency>
                <groupId>com.alibaba.blink</groupId>
                <artifactId>blink-connector-common</artifactId>
                <version>blink-3.2.1-SNAPSHOT</version>
                <scope>provided</scope>
              </dependency>
              <dependency>
                <groupId>com.alibaba.blink</groupId>
                <artifactId>blink-connector-custom</artifactId>
                <version>blink-3.2.1-SNAPSHOT</version>
                <scope>provided</scope>
              </dependency>
              <dependency>
                <groupId>com.alibaba.blink</groupId>
                <artifactId>flink-table_2.11</artifactId>
                <version>blink-3.2.1-SNAPSHOT</version>
                <scope>provided</scope>
              </dependency>
            </dependencies>
            ```

    -   Realtime Compute for Apache Flink V2.0
        -   JAR packages that you need to download
            -   [blink-connector-common-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1544614396864/blink-connector-custom-blink-2.2.4.jar)
            -   [blink-connector-custom-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1544614508576/blink-connector-common-blink-2.2.4.jar)
            -   [blink-table-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1544614551435/blink-table-blink-2.2.4.jar)
            -   [flink-table\_2.11-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1544614593263/flink-table_2.11-blink-2.2.4-20181102.033727-1.jar)
            -   [flink-core-blink-2.2.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1547195750660/flink-core-blink-2.2.4.jar)
        -   Dependencies

            ```
              <dependencies>
                <dependency>
                  <groupId>com.alibaba.blink</groupId>
                  <artifactId>blink-table</artifactId>
                  <version>blink-2.2.4-SNAPSHOT</version>
                  <scope>provided</scope>
                </dependency>
                <dependency>
                  <groupId>org.apache.flink</groupId>
                  <artifactId>flink-table_2.11</artifactId>
                  <version>blink-2.2.4-SNAPSHOT</version>
                  <scope>provided</scope>
                </dependency>
                <dependency>
                  <groupId>org.apache.flink</groupId>
                  <artifactId>flink-core</artifactId>
                  <version>blink-2.2.4-SNAPSHOT</version>
                  <scope>provided</scope>
                </dependency>
                <dependency>
                  <groupId>com.alibaba.blink</groupId>
                  <artifactId>blink-connector-common</artifactId>
                  <version>blink-2.2.4-SNAPSHOT</version>
                  <scope>provided</scope>
                </dependency>
                <dependency>
                  <groupId>com.alibaba.blink</groupId>
                  <artifactId>blink-connector-custom</artifactId>
                  <version>blink-2.2.4-SNAPSHOT</version>
                  <scope>provided</scope>
                </dependency>
              </dependencies>
            ```

    -   Realtime Compute for Apache Flink V1.0
        -   JAR packages that you need to download
            -   [blink-connector-common-blink-1.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1568700794800/blink-connector-common-blink-1.4-SNAPSHOT.jar)
            -   [blink-connector-custom-blink-1.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1568700836064/blink-connector-custom-blink-1.4-SNAPSHOT.jar)
            -   [blink-table-blink-1.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1568700856149/blink-table-blink-1.4-SNAPSHOT.jar)
            -   [flink-core-blink-1.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1568700884035/flink-core-blink-1.4-SNAPSHOT.jar)
            -   [flink-streaming-scala\_2.11-blink-1.4](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/99987/cn_zh/1568700899889/flink-streaming-scala_2.11-blink-1.4-SNAPSHOT.jar)
        -   Dependencies

            ```
                  <dependencies>
                    <dependency>
                        <groupId>com.alibaba.blink</groupId>
                        <artifactId>blink-connector-common</artifactId>
                        <version>blink-1.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>com.alibaba.blink</groupId>
                        <artifactId>blink-connector-custom</artifactId>
                        <version>blink-1.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-streaming-scala_${scala.binary.version}</artifactId>
                        <version>blink-1.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>org.apache.flink</groupId>
                        <artifactId>flink-core</artifactId>
                        <version>blink-1.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                    <dependency>
                        <groupId>com.alibaba.blink</groupId>
                        <artifactId>blink-table</artifactId>
                        <version>blink-1.4-SNAPSHOT</version>
                        <scope>provided</scope>
                    </dependency>
                </dependencies>
            ```


## API description

The class of a custom result table must inherit the CustomSinkBase base class of the custom sink plug-in and is implemented by using the following methods:

```
protected Map<String,String> userParamsMap;// userParamsMap is the key-value pair defined in the WITH clause of custom SQL statements. All keys are in lowercase letters.
protected Set<String> primaryKeys;// primaryKeys is the custom primary key field.
protected List<String> headerFields;// headerFields is the list of fields marked as header.
protected RowTypeInfo rowTypeInfo;// rowTypeInfo indicates the field type and name.
/**
 * The initialization method. This method is called when you create a table for the first time or when a failover occurs.
 * 
 * @param taskNumber The serial number of the current node.
 * @ param numTasks The total number of sink nodes.
 * @throws IOException
 */
public abstract void open(int taskNumber,int numTasks) throws IOException;

/**
 * The close method that is used to release resources.
 *
 * @throws IOException
 */
public abstract void close() throws IOException;

/**
 * Insert a single row of data.
 *
 * @param row
 * @throws IOException
 */
public abstract void writeAddRecord(Row row) throws IOException;

/**
 * Delete a single row of data.
 *
 * @param row
 * @throws IOException
 */
public abstract void writeDeleteRecord(Row row) throws IOException;

/**
 * If you want to use this method to insert multiple rows of data at the same time, you must load all data cached in the threads to the downstream storage system. If you do not need to insert multiple rows of data at the same time, this method is not required.
 *
 * @throws IOException
 */
public abstract void sync() throws IOException;

/** 
* Return the class name. 
*/ 
public String getName();
```

## Example of creating a custom ApsaraDB for Redis result table

Download [Demo of Realtime Compute for Apache Flink V3.0](https://github.com/RealtimeCompute/blink_customersink_3x). Go to the blink\_customersink\_3x directory, run the `mvn clean package` command, and then upload the JAR package blink\_customersink\_3x/target/blink-customersink-3.x-1.0-SNAPSHOT-jar-with-dependencies.jar that is compiled in the Realtime Compute for Apache Flink console. After you reference required resources, you must specify `type = 'custom'` for the custom sink plug-in, and specify the class for implementing the API.

**Note:** This example is only used as a reference for developing custom result tables. It is not suitable for production purposes.

```
create table in_table(
    kv varchar 
)with(
    type = 'random'
);

create table out_table(
    `key` varchar,
    `value` varchar
)with(
    type = 'custom',
    class = 'com.alibaba.blink.customersink.RedisSink',
    -- 1. You can define more custom parameters. These parameters can be obtained by using userParamsMap in the open function.
    -- 2. The keys for the parameters in the WITH clause are not case-sensitive. In Realtime Compute for Apache Flink, the values of the parameter keys are processed as lowercase letters. We recommend that you declare keys in lowercase letters in the data definition language (DDL) statements that reference the data store.
    host = 'r-uf****.redis.rds.aliyuncs.com',
    port = '6379',
    db = '0',
    batchsize = '10',
    password = '<yourHostPassword>'
);

insert into out_table
select
substring(kv,0,4) as `key`,
substring(kv,0,6) as `value`
from in_table;
```

The following table describes the parameters of the plug-in of the ApsaraDB for Redis sink.

|Parameter|Description|Required|Remarks|
|---------|-----------|--------|-------|
|host|The internal endpoint of the ApsaraDB for Redis instance.|Yes|None.|
|port|The number of the port that is used to access the ApsaraDB for Redis instance.|Yes|None.|
|password|The password that is used to access the ApsaraDB for Redis instance.|Yes|None.|
|db|The serial number of an ApsaraDB for Redis database.|No|Default value: 0. This value indicates db0.|
|batchsize|The number of data records that can be written at a time.|No|Default value: 1. This value indicates that multiple data records cannot be written at a time.|

