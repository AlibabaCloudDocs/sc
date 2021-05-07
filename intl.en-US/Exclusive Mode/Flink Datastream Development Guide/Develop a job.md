# Develop a job

This topic describes the POM dependency used to develop DataStream jobs, example of DataStream job development, and DataStream connectors.

**Note:**

-   Only Blink 3.2.2 and later versions of Realtime Compute for Apache Flink in exclusive mode supports Flink DataStream.
-   We recommend that you use a Maven project of IntelliJ IDEA to develop a DataStream job.
-   To avoid JAR dependency conflicts, take note of the following points:
    -   Select the Blink version on the Development page the same as the Blink version of POM dependencies.
    -   Specify `<scope>provided</scope>` for Blink-related dependencies.
    -   Use the Shade plug-in to package other third-party dependencies. For more information, see [Apache Maven Shade plug-in](https://maven.apache.org/plugins/maven-shade-plugin/index.html).

## POM dependency

Add a [POM dependency](https://search.maven.org/search?q=com.alibaba.blink) based on the Blink version of the job that is running. The following example shows the POM file used for Blink 3.4.0.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.alibaba.blink</groupId>
    <artifactId>blink-datastreaming</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <scala.version>2.11.12</scala.version>
        <scala.binary.version>2.11</scala.binary.version>
        <blink.version>blink-3.4.0</blink.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.alibaba.blink</groupId>
            <artifactId>flink-streaming-java_${scala.binary.version}</artifactId>
            <version>${blink.version}</version>
            <scope>provided</scope>
        </dependency>

<!--        Add test framework-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>2.11.12</version>
        </dependency>

<!--        Add logging framework-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.7</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
            <scope>runtime</scope>
        </dependency>
    </dependencies>



    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <manifestEntries>
                                        <Main-Class>your main class</Main-Class>
                                        <X-Compile-Source-JDK>${maven.compiler.source}</X-Compile-Source-JDK>
                                        <X-Compile-Target-JDK>${maven.compiler.target}</X-Compile-Target-JDK>
                                    </manifestEntries>
                                </transformer>
                            </transformers>
                            <relocations combine.self="override">
                                <relocation>
                                    <pattern>XXX</pattern>
                                    <shadedPattern>shaded.XXX</shadedPattern>
                                </relocation>
                            </relocations>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

**Note:** If you want to use Snapshot, you can add the [POM dependency](https://oss.sonatype.org/content/repositories/snapshots/com/alibaba/blink/flink-core/) based on your Snapshot version.

## DataStream connectors

The following DataStream connectors are added to Blink 3.2:

-   Kafka
-   Kafka \(open source version\)
-   HBase \(open source version\)
-   JDBC
-   RDS SINK
-   Elasticsearch
-   MongoDB
-   Redis

**Note:** Some DataStream connectors have been open-sourced. For more information, see [alibaba-flink-connectors](https://github.com/alibaba/alibaba-flink-connectors).

