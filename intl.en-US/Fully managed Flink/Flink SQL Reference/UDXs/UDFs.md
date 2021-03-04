# UDFs

This topic describes how to create, register, and use a user-defined scalar function \(UDF\) in Realtime Compute for Apache Flink.

## Definition

A UDF maps zero, one, or multiple scalar values to a new scalar value. The input and output of a UDF are mapped in a one-to-one relationship. Each time a UDF reads a row of data, it writes an output value.

## Create a UDF

**Note:** Realtime Compute for Apache Flink provides examples of user-defined extensions \(UDXs\) to facilitate your business development. Flink UDX examples of how to implement UDFs, user-defined aggregate functions \(UDAFs\), and user-defined table-valued functions \(UDTFs\) are included. The development environment of the corresponding version is configured in each example.

1.  Download and decompress [ASI\_UDX\_Demo](https://github.com/RealtimeCompute/ASI_UDX) to an on-premises machine.

    After you decompress the package, an ASI\_UDX-main folder is generated. The folder contains the following files:

    -   pom.xml: a project-level configuration file that describes the Maven coordinates, dependencies, rules that developers must follow, defect management system, organizations, and licenses of a project, as well as all other project-related factors.
    -   \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDF\\ASI\_UDF.java: the Java code for the sample UDF.
2.  Open IntelliJ IDEA and choose **File** \> **Open**. Select the extracted ASI\_UDX-main folder and click OK.
3.  Double-click the ASI\_UDF.java file in the \\ASI\_UDX-main\\src\\main\\java\\ASI\_UDF directory, and make configurations in the file based on your business needs.

    In this example, ASI\_UDF.java is configured with code to obtain the characters from the begin position to the end position in each data record.

    ```
    package ASI_UDF;
    
    import org.apache.flink.table.functions.ScalarFunction;
    
    public class ASI_UDF extends ScalarFunction {
        public String eval(String s, Integer begin, Integer end) {
            return s.substring(begin, end);
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

    If the ASI\_UDX-1.0-SNAPSHOT.jar package appears in the \\ASI\_UDX-main\\target\\ directory, the UDF is created.


## Register a UDF

For more information about how to register a UDF, see [Manage UDFs]().

## Use a UDF

After a UDF is registered, you can perform the following steps to use it:

1.  Use Flink SQL to create a job. For more information, see [Develop a job]().

    The following code provides an example on how to obtain the characters from position 2 to 4 of the string in each row of the a field in the ASI\_UDF\_Source table:

    ```
    CREATE TEMPORARY TABLE ASI_UDF_Source (
      a VARCHAR,
      b INT,
      c INT
    ) WITH (
      'connector' = 'datagen'
    );
    
    CREATE TEMPORARY TABLE ASI_UDF_Sink (
      a VARCHAR
    ) WITH (
      'connector' = 'blackhole'
    );
    
    INSERT INTO ASI_UDF_Sink
    SELECT ASI_UDF('a',2,4)
    FROM ASI_UDF_Source;
    ```

2.  On the **Jobs** page, find the job for which you want to use the UDF and click **Start** in the **Actions** column.

    After the job is started, each row of the ASI\_UDF\_Sink table is inserted with the characters from position 2 to 4 of the string in each row of the a field in the ASI\_UDF\_Source table.


