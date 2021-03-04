# UDTFs

This topic describes how to create, register, and use a user-defined table-valued function \(UDTF\) in Realtime Compute for Apache Flink.

## Definition

A UDTF takes zero, one, or multiple scalar values as input parameters. These parameters can be variable-length parameters. UDTFs are similar to UDFs but differ in that they return any number of rows instead of a single value. Returned rows can consist of one or more columns. A function invocation can return data in multiple rows or columns.

## Create a UDTF

**Note:** Realtime Compute for Apache Flink provides examples of user-defined extensions \(UDXs\) to facilitate your business development. Flink UDX examples of how to implement UDFs, user-defined aggregate functions \(UDAFs\), and user-defined table-valued functions \(UDTFs\) are included. The development environment of the corresponding version is configured in each example.

1.  Download and decompress [ASI\_UDX\_Demo](https://github.com/RealtimeCompute/ASI_UDX) to an on-premises machine.

    After you decompress the package, an ASI\_UDX-main folder is generated. The folder contains the following files:

    -   pom.xml: a project-level configuration file that describes the Maven coordinates, dependencies, rules that developers must follow, defect management system, organizations, and licenses of a project, as well as all other project-related factors.
    -   \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDF\\ASI\_UDTF.java: the Java code for the sample UDTF.
2.  Open IntelliJ IDEA and choose **File** \> **Open**. Select the extracted ASI\_UDX-main folder and click OK.
3.  Double-click the ASI\_UDTF.java file in the \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDTF directory, and make configurations in the file based on your business needs.

    In this example, ASI\_UDTF.java is configured with code to split a string in a row into multiple columns of strings separated by vertical bars \(\|\).

    ```
    package ASI_UDTF;
    
    import org.apache.flink.api.java.tuple.Tuple2;
    import org.apache.flink.table.functions.TableFunction;
    
    public class ASI_UDTF extends TableFunction<Tuple2<String,String>> {
        public void eval(String str){
            String[] split = str.split("\\|");
            String name = split[0];
            String place = split[1];
            Tuple2<String,String> tuple2 = Tuple2.of(name,place);
            collect(tuple2);
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
                <! --<scope>provided</scope>-->
            </dependency>
            <dependency>
                <groupId>org.apache.flink</groupId>
                <artifactId>flink-table</artifactId>
                <version>1.11.0</version>
                <type>pom</type>
                <! --<scope>provided</scope>-->
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

    If the ASI\_UDX-1.0-SNAPSHOT.jar package appears in the \\ASI\_UDX-main\\target\\ directory, the UDTF is created.


## Register a UDTF

For more information about how to register a UDTF, see [Manage UDFs]().

## Use a UDTF

After a UDTF is registered, you can perform the following steps to use it:

1.  Use Flink SQL to create a job. For more information, see [Develop a job]().

    In the ASI\_UDTF\_Source table, the string in each row of the message field is split into multiple columns separated by vertical bars \(\|\). The following code provides an example:

    ```
    CREATE TEMPORARY TABLE ASI_UDTF_Source (
      `message`  VARCHAR
    ) WITH (
      'connector'='datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDTF_Sink (
      name  VARCHAR,
      place  VARCHAR
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDTF_Sink
    SELECT name,place
    FROM ASI_UDTF_Source,lateral table(ASI_UDTF(`message`)) as T(name,place);
    ```

2.  On the **Jobs** page, find the job for which you want to use the UDTF and click **Start** in the **Actions** column.

    After the job is started, the ASI\_UDTF\_Sink table is inserted with the multiple columns of strings of the message field in the ASI\_UDTF\_Source table. These columns of strings are separated by vertical bars \(\|\).


