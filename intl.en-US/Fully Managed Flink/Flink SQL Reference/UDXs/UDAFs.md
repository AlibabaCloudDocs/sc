# UDAFs

This topic describes how to create, register, and use a user-defined aggregate function \(UDAF\) in Realtime Compute for Apache Flink.

## Definition

A UDAF aggregates multiple values into a single value. A many-to-one mapping is established between the input and output of a UDAF. Multiple input records are aggregated to generate one output value.

## Create a UDAF

**Note:** Realtime Compute for Apache Flink provides examples of user-defined extensions \(UDXs\) to facilitate your business development. Flink UDX examples of how to implement UDFs, user-defined aggregate functions \(UDAFs\), and user-defined table-valued functions \(UDTFs\) are included. The development environment of the related version is configured in each example.

1.  Download and decompress [ASI\_UDX\_Demo](https://github.com/RealtimeCompute/ASI_UDX) to your on-premises machine.

    After you decompress the package, the ASI\_UDX-main folder is generated. The folder contains the following files:

    -   pom.xml: a project-level configuration file that describes the Maven coordinates, dependencies, rules that developers must follow, defect management system, organizations, and licenses of a project, as well as all other project-related factors.
    -   \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDAF\\ASI\_UDAF.java: the Java code for the sample UDAF.
2.  Open IntelliJ IDEA and choose **File** \> **Open**. Select the extracted ASI\_UDX-main folder and click OK.
3.  Double-click the ASI\_UDAF.java file in the \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDAF directory, and make configurations in the file based on your business needs.

    In this example, ASI\_UDAF.java is configured with code to sum current data and historical data. For example, if the first value is 1, the second value is 2, and the third value is 3, 1, 3, and 6 are returned.

    ```
    package ASI_UDAF;
    
    import org.apache.flink.table.functions.AggregateFunction;
    public class ASI_UDAF{
        public static class AcSum {
            public long sum;
        }
    
        public static class WeightedSum extends AggregateFunction<Long, AcSum> {
    
            @Override
            public Long getValue(AcSum acSum) {
                return acSum.sum;
            }
            @Override
            public AcSum createAccumulator() {
                AcSum acCount = new AcSum();
                acCount.sum = 0;
                return acCount;
            }
            public void accumulate(AcSum acc, long acSum) {
                acc.sum += acSum;
            }
        }
    }
    ```

4.  Double-click the pom.xml file in the \\ASI\_UDX-main\\ directory and make configurations in the file.

    In this example, pom.xml is configured with the information of main JAR dependencies of Apache Flink 1.11. Perform one of the following operations based on your business needs:

    -   If your business does not depend on other JAR packages, proceed to the next step without configuring the pom.xml file.
    -   If your business depends on other JAR packages, add information of the required JAR packages to the pom.xml file.
    Apache Flink 1.11 depends on the following JAR packages:

    ```
    <dependencies>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-streaming-java_2.12</artifactId>
                <version>1.11.0</version>
                <!--<scope>provided</scope>-->
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-table</artifactId>
                <version>1.11.0</version>
                <type>pom</type>
                <!--<scope>provided</scope>-->
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-core</artifactId>
                <version>1.11.0</version>
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-table-common</artifactId>
                <version>1.11.0</version>
            </dependency>
        </dependencies>
    ```

5.  Go to the directory where the pom.xml file is stored. Then, run the following command to package the file:

    ```
    mvn package -Dcheckstyle.skip
    ```

    If the ASI\_UDX-1.0-SNAPSHOT.jar package appears in the \\ASI\_UDX-main\\target\\ directory, the UDAF is created.


## Register a UDAF

For more information about how to register a UDAF, see [Register a UDF](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Manage UDFs.md)

## Use a UDAF

After a UDAF is registered, you can perform the following steps to use it:

1.  Use Flink SQL to create a job. For more information, see [Develop a job](/intl.en-US/Fully Managed Flink/Flink SQL Developer Guide/Develop a job.md).

    The following code provides an example on how to obtain the sum of the current data and historical data of the a field in the ASI\_UDAF\_Source table:

    ```
    CREATE TEMPORARY TABLE ASI_UDAF_Source (
      a   BIGINT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDAF_Sink (
      sum  BIGINT
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDAF_Sink
    SELECT `ASI_UDAF$WeightedSum`(a)
    FROM ASI_UDAF_Source;
    ```

2.  On the **Jobs** page, find the job for which you want to use the UDAF and click **Start** in the **Actions** column.

    After the job is started, each row in the ASI\_UDAF\_Sink table is inserted with the sum of the current data and historical data of the a field in the ASI\_UDAF\_Source table.


