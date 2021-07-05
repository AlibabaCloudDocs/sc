---
keyword: [configure logs, configure job logs]
---

# Configure job logs

This topic describes how to specify that job logs are exported to Object Storage Service \(OSS\) and Log Service.

If you need to view the operational logs of the current running job, click **Flink UI**.

If the following cases occur, you must specify that the operational logs of jobs are exported to external storage \(OSS or Log Service\) and start the jobs.

-   You need to view, search for, and analyze the historical operational logs of jobs. By default, the system retains the latest 5 MB operational logs.
-   The Flink UI fails to be accessed. You need to check JobManager logs to locate the error.

    **Note:**

    -   If the TaskManager or JobManager is started, logs are written to Log Service or OSS. However, if the TaskManager and JobManager are not started as expected, logs are not written to Log Service or OSS.
    -   If you cannot find the relevant log file on OSS because the JobManager and TaskManager are not started as expected,[submit a ticket](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A//ticket-intl.console.aliyun.com/%23).

This topic describes how to configure job logs in the following scenarios:

-   [Specify that the logs of a single new job are exported to OSS](#section_jqb_mq4_7m7)
-   [Specify that the logs of a single existing job are exported to OSS](#section_n6c_xsy_592)
-   [Specify that all the job logs in a workspace are exported to OSS](#section_na9_lsd_wqg)
-   [Specify that the logs of a single new job are exported to Log Service](#section_bqc_f3w_d5n)
-   [Specify that the logs of a single existing job are exported to Log Service](#section_4q9_16m_s52)
-   [Specify that all the job logs in a workspace are exported to Log Service](#section_dkn_3jb_s94)

## Specify that the logs of a single new job are exported to OSS

1.  Go to the Draft Editor page and configure job information.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Draft Editor**.

    4.  Click **New**.

    5.  In the New Draft dialog box, configure job parameters. On the Draft Editor page, enter SQL code or upload a JAR package.

2.  Configure job logs.

    1.  On the right side of the Draft Editor page, click the **Advanced** tab.

    2.  In the **Logging** section, set **Logging Profile** to **Custom Template**.

    3.  Paste the following content into the input box and specify the parameters in the following table based on your OSS bucket information.

        -   Per-Job Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="20 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="4"/> 
                </Appender>  
                <Appender name="OSS" type="OSS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="baseUri">oss://YOUR-BUCKET-NAME/</Property>
                  <Property name="endpoint">https://YOUR-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-OSS-ACCESSKEYID</Property>
                  <Property name="accessKeySecret">YOUR-OSS-ACCESSKEYSECRET</Property>
                  <Property name="flushIntervalSeconds">10</Property>  
                  <Property name="flushIntervalEventCount">100</Property>  
                  <Property name="rollingBytes">10485760</Property>  
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="OSS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        -   Session Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="20 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="4"/> 
                </Appender>  
                <Appender name="OSS" type="OSS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="baseUri">oss://YOUR-BUCKET-NAME/</Property>
                  <Property name="endpoint">https://YOUR-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-OSS-ACCESSKEYID</Property>
                  <Property name="accessKeySecret">YOUR-OSS-ACCESSKEYSECRET</Property>
                  <Property name="flushIntervalSeconds">10</Property>  
                  <Property name="flushIntervalEventCount">100</Property>  
                  <Property name="rollingBytes">10485760</Property> 
                  <Property name="deploymentId">{{ sessionClusterName }}</Property>
                  <Property name="jobId">{{ sessionClusterId }}</Property> 
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="OSS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        |Parameter|Description|
        |---------|-----------|
        |YOUR-BUCKET-NAME|Replace the value of this parameter with the name of your OSS bucket.|
        |YOUR-ENDPOINT|Replace the value of this parameter with the endpoint of your OSS bucket. Replace the value of this parameter with **Endpoint** information in the row where **VPC Access from ECS \(Internal Network\)** is located. |
        |YOUR-OSS-ACCESSKEYID|Replace the value of this parameter with the **AccessKey ID** of the account that you use to configure OSS. **Note:** If you configure OSS within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure OSS within a different account from that of a fully managed cluster, this parameter is required. |
        |YOUR-OSS-ACCESSKEYSECRET|Replace the value of this parameter with the **AccessKey secret** of the account that you use to configure OSS. **Note:** If you configure OSS within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure OSS within a different account from that of a fully managed cluster, this parameter is required. |
        |sessionClusterName|Replace the value of this parameter with the name of a session cluster.|
        |sessionClusterId|Replace the value of this parameter with the ID of a session cluster.|

3.  Start the job.

    1.  In the upper-right corner of the Draft Editor page, click **Validate**.

        Write complete SQL logic before you click **Validate**. Otherwise, the verification fails.

    2.  Click **Publish**.

    3.  In the left-side navigation pane, click **Deployments**.

    4.  Find the job whose logs you want to export to OSS, and click **Start** in the **Actions** column.

        You can view the job logs in the OSS console. For more information, see [View job logs on OSS](/intl.en-US/Fully Managed Flink/O&M management/View job logs.md).


## Specify that the logs of a single existing job are exported to OSS

1.  Go to the Deployments page.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Deployments**.

2.  Configure job logs.

    1.  Click the name of the job whose logs you want to export to OSS.

    2.  In the upper-right corner of the job details page, click **Configure**.

    3.  On the right side of the Draft Editor page, click the **Advanced** tab.

    4.  In the **Logging** section, set **Logging Profile** to **Custom Template**.

    5.  Paste the following content into the input box and specify the parameters in the following table based on your OSS bucket information.

        -   Per-Job Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="20 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="4"/> 
                </Appender>  
                <Appender name="OSS" type="OSS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="baseUri">oss://YOUR-BUCKET-NAME/</Property>
                  <Property name="endpoint">https://YOUR-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-OSS-ACCESSKEYID</Property>
                  <Property name="accessKeySecret">YOUR-OSS-ACCESSKEYSECRET</Property>
                  <Property name="flushIntervalSeconds">10</Property>  
                  <Property name="flushIntervalEventCount">100</Property>  
                  <Property name="rollingBytes">10485760</Property>  
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="OSS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        -   Session Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="20 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="4"/> 
                </Appender>  
                <Appender name="OSS" type="OSS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="baseUri">oss://YOUR-BUCKET-NAME/</Property>
                  <Property name="endpoint">https://YOUR-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-OSS-ACCESSKEYID</Property>
                  <Property name="accessKeySecret">YOUR-OSS-ACCESSKEYSECRET</Property>
                  <Property name="flushIntervalSeconds">10</Property>  
                  <Property name="flushIntervalEventCount">100</Property>  
                  <Property name="rollingBytes">10485760</Property> 
                  <Property name="deploymentId">{{ sessionClusterName }}</Property>
                  <Property name="jobId">{{ sessionClusterId }}</Property> 
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="OSS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        |Parameter|Description|
        |---------|-----------|
        |YOUR-BUCKET-NAME|Replace the value of this parameter with the name of your OSS bucket.|
        |YOUR-ENDPOINT|Replace the value of this parameter with the endpoint of your OSS bucket. Replace the value of this parameter with **Endpoint** information in the row where **VPC Access from ECS \(Internal Network\)** is located. |
        |YOUR-OSS-ACCESSKEYID|Replace the value of this parameter with the **AccessKey ID** of the account that you use to configure OSS. **Note:** If you configure OSS within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure OSS within a different account from that of a fully managed cluster, this parameter is required. |
        |YOUR-OSS-ACCESSKEYSECRET|Replace the value of this parameter with the **AccessKey secret** of the account that you use to configure OSS. **Note:** If you configure OSS within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure OSS within a different account from that of a fully managed cluster, this parameter is required. |
        |sessionClusterName|Replace the value of this parameter with the name of a session cluster.|
        |sessionClusterId|Replace the value of this parameter with the ID of a session cluster.|

    6.  Click **Save**.

3.  In the left-side navigation pane, click **Deployments**.

4.  Find the job whose logs you want to export to OSS, and click **Start** in the **Actions** column.

    You can view the logs of the job in the OSS console. For more information, see [View job logs on OSS](/intl.en-US/Fully Managed Flink/O&M management/View job logs.md).


## Specify that all the job logs in a workspace are exported to OSS

You can configure **Deployment Defaults** to specify that all the job logs in a workspace are exported to OSS by default.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  Configure job logs.

    1.  In the left-side navigation pane, choose **Administration** \> **Deployment Defaults**.

    2.  In the **Logging** section, set **Logging Profile** to **Custom Template**.

    3.  Paste the following content into the input box and specify the parameters in the following table based on your OSS bucket information.

        -   Per-Job Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="20 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="4"/> 
                </Appender>  
                <Appender name="OSS" type="OSS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="baseUri">oss://YOUR-BUCKET-NAME/</Property>
                  <Property name="endpoint">https://YOUR-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-OSS-ACCESSKEYID</Property>
                  <Property name="accessKeySecret">YOUR-OSS-ACCESSKEYSECRET</Property>
                  <Property name="flushIntervalSeconds">10</Property>  
                  <Property name="flushIntervalEventCount">100</Property>  
                  <Property name="rollingBytes">10485760</Property>  
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="OSS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        -   Session Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="20 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="4"/> 
                </Appender>  
                <Appender name="OSS" type="OSS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="baseUri">oss://YOUR-BUCKET-NAME/</Property>
                  <Property name="endpoint">https://YOUR-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-OSS-ACCESSKEYID</Property>
                  <Property name="accessKeySecret">YOUR-OSS-ACCESSKEYSECRET</Property>
                  <Property name="flushIntervalSeconds">10</Property>  
                  <Property name="flushIntervalEventCount">100</Property>  
                  <Property name="rollingBytes">10485760</Property> 
                  <Property name="deploymentId">{{ sessionClusterName }}</Property>
                  <Property name="jobId">{{ sessionClusterId }}</Property> 
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="OSS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        |Parameter|Description|
        |---------|-----------|
        |YOUR-BUCKET-NAME|Replace the value of this parameter with the name of your OSS bucket.|
        |YOUR-ENDPOINT|Replace the value of this parameter with the endpoint of your OSS bucket. Replace the value of this parameter with **Endpoint** information in the row where **VPC Access from ECS \(Internal Network\)** is located. |
        |YOUR-OSS-ACCESSKEYID|Replace the value of this parameter with the **AccessKey ID** of the account that you use to configure OSS. **Note:** If you configure OSS within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure OSS within a different account from that of a fully managed cluster, this parameter is required. |
        |YOUR-OSS-ACCESSKEYSECRET|Replace the value of this parameter with the **AccessKey secret** of the account that you use to configure OSS. **Note:** If you configure OSS within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure OSS within a different account from that of a fully managed cluster, this parameter is required. |
        |sessionClusterName|Replace the value of this parameter with the name of a session cluster.|
        |sessionClusterId|Replace the value of this parameter with the ID of a session cluster.|

    4.  Click **Save Changes**.

        **Note:** After you configure **Deployment Defaults**, the logs of all the jobs that are created in the workspace are stored in OSS. You can view the logs of the job in the OSS console. For more information, see [View job logs on OSS](/intl.en-US/Fully Managed Flink/O&M management/View job logs.md).


## Specify that the logs of a single new job are exported to Log Service

1.  Go to the Draft Editor page and configure job information.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Draft Editor**.

    4.  Click **New**.

    5.  In the New Draft dialog box, configure job parameters. On the Draft Editor page, enter SQL code or upload a JAR package.

2.  Configure job logs.

    1.  On the right side of the Draft Editor page, click the **Advanced** tab.

    2.  In the **Logging** section, set **Logging Profile** to **Custom Template**.

    3.  Paste the following content into the code editor of **Custom Template** and specify the parameters in the following table based on your Log Service information.

        -   Per-Job Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="5 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="1"/> 
                </Appender>  
                <Appender name="SLS" type="SLS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
            
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="project">YOUR-SLS-PROJECT</Property>  
                  <Property name="logStore">YOUR-SLS-LOGSTORE</Property> 
                  <Property name="endpoint">YOUR-SLS-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-SLS-ACCESSKEYID</Property> 
                  <Property name="accessKeySecret">YOUR-SLS-ACCESSKEYSECRET</Property> 
                  <Property name="topic">{{ namespace }}:{{ deploymentId }}:{{ jobId }}</Property> 
                  <Property name="flushIntervalSeconds">10</Property>
                  <Property name="flushIntervalEventCount">100</Property>
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="SLS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        -   Session Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="5 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="1"/> 
                </Appender>  
                <Appender name="SLS" type="SLS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
            
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="project">YOUR-SLS-PROJECT</Property>  
                  <Property name="logStore">YOUR-SLS-LOGSTORE</Property> 
                  <Property name="endpoint">YOUR-SLS-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-SLS-ACCESSKEYID</Property> 
                  <Property name="accessKeySecret">YOUR-SLS-ACCESSKEYSECRET</Property> 
                  <Property name="topic">{{ namespace }}:{{ sessionClusterName }}:{{ sessionClusterId }}</Property> 
                  <Property name="flushIntervalSeconds">10</Property>
                  <Property name="flushIntervalEventCount">100</Property>
                  <Property name="deploymentId">{{ sessionClusterName }}</Property>
                  <Property name="jobId">{{ sessionClusterId }}</Property>
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="SLS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        |Parameter|Description|
        |---------|-----------|
        |YOUR-SLS-PROJECT|Replace the value of this parameter with the project name of your Log Service.|
        |YOUR-SLS-LOGSTORE|Replace the value of this parameter with the Logstore name of your Log Service.|
        |YOUR-SLS-ENDPOINT|Replace the value of this parameter with the endpoint of the region where your Log Service resides. For more information, see [Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md).|
        |YOUR-SLS-ACCESSKEYID|Replace the value of this parameter with the **AccessKey ID** of the account that you use to configure Log Service. **Note:** If you configure Log Service within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure Log Service within a different account from that of a fully managed cluster, this parameter is required. |
        |YOUR-SLS-ACCESSKEYSECRET|Replace the value of this parameter with the **AccessKey secret** of the account that you use to configure Log Service. **Note:** If you configure Log Service within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure Log Service within a different account from that of a fully managed cluster, this parameter is required. |
        |sessionClusterName|Replace the value of this parameter with the name of a session cluster.|
        |sessionClusterId|Replace the value of this parameter with the ID of a session cluster.|

        **Note:**

        -   Make sure that the **AccessKey ID** and **AccessKey secret** are the key pair managed within the account that you use to activate **fully managed Flink**.
        -   Make sure that the account you use to activate **fully managed Flink** has the permissions to read and write the project of Log Service.
3.  Start the job.

    1.  In the upper-right corner of the Draft Editor page, click **Validate**.

    2.  Click **Publish**.

    3.  In the left-side navigation pane, click **Deployments**.

    4.  Find the job whose logs you want to export to Log Service, and click **Start** in the **Actions** column.

        You can view the logs of the job in the Log Service console. For more information, see [View job logs on Log Service](/intl.en-US/Fully Managed Flink/O&M management/View job logs.md).


## Specify that the logs of a single existing job are exported to Log Service

1.  Go to the Deployments page.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

    2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

    3.  In the left-side navigation pane, click **Deployments**.

2.  Configure job logs.

    1.  Click the name of the job whose logs you want to export to Log Service.

    2.  In the upper-right corner of the job details page, click **Configure**.

    3.  On the right side of the Draft Editor page, click the **Advanced** tab.

    4.  In the **Logging** section, set **Logging Profile** to **Custom Template**.

    5.  Paste the following content into the code editor of **Custom Template** and specify the parameters in the following table based on your Log Service information.

        -   Per-Job Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="5 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="1"/> 
                </Appender>  
                <Appender name="SLS" type="SLS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
            
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="project">YOUR-SLS-PROJECT</Property>  
                  <Property name="logStore">YOUR-SLS-LOGSTORE</Property> 
                  <Property name="endpoint">YOUR-SLS-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-SLS-ACCESSKEYID</Property> 
                  <Property name="accessKeySecret">YOUR-SLS-ACCESSKEYSECRET</Property> 
                  <Property name="topic">{{ namespace }}:{{ deploymentId }}:{{ jobId }}</Property> 
                  <Property name="flushIntervalSeconds">10</Property>
                  <Property name="flushIntervalEventCount">100</Property>
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="SLS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        -   Session Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="5 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="1"/> 
                </Appender>  
                <Appender name="SLS" type="SLS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
            
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="project">YOUR-SLS-PROJECT</Property>  
                  <Property name="logStore">YOUR-SLS-LOGSTORE</Property> 
                  <Property name="endpoint">YOUR-SLS-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-SLS-ACCESSKEYID</Property> 
                  <Property name="accessKeySecret">YOUR-SLS-ACCESSKEYSECRET</Property> 
                  <Property name="topic">{{ namespace }}:{{ sessionClusterName }}:{{ sessionClusterId }}</Property> 
                  <Property name="flushIntervalSeconds">10</Property>
                  <Property name="flushIntervalEventCount">100</Property>
                  <Property name="deploymentId">{{ sessionClusterName }}</Property>
                  <Property name="jobId">{{ sessionClusterId }}</Property>
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="SLS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        |Parameter|Description|
        |---------|-----------|
        |YOUR-SLS-PROJECT|Replace the value of this parameter with the project name of your Log Service.|
        |YOUR-SLS-LOGSTORE|Replace the value of this parameter with the Logstore name of your Log Service.|
        |YOUR-SLS-ENDPOINT|Replace the value of this parameter with the endpoint of the region where your Log Service resides. For more information, see [Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md).|
        |YOUR-SLS-ACCESSKEYID|Replace the value of this parameter with the **AccessKey ID** of the account that you use to configure Log Service. **Note:** If you configure Log Service within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure Log Service within a different account from that of a fully managed cluster, this parameter is required. |
        |YOUR-SLS-ACCESSKEYSECRET|Replace the value of this parameter with the **AccessKey secret** of the account that you use to configure Log Service. **Note:** If you configure Log Service within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure Log Service within a different account from that of a fully managed cluster, this parameter is required. |
        |sessionClusterName|Replace the value of this parameter with the name of a session cluster.|
        |sessionClusterId|Replace the value of this parameter with the ID of a session cluster.|

        **Note:**

        -   Make sure that the **AccessKey ID** and **AccessKey secret** are the key pair managed within the account that you use to activate **fully managed Flink**.
        -   Make sure that the account you use to activate **fully managed Flink** has the permissions to read and write the project of Log Service.
    6.  Click **Save**.

3.  In the left-side navigation pane, click **Deployments**.

4.  Find the job whose logs you want to export to Log Service, and click **Start** in the **Actions** column.

    You can view the logs of the job in the Log Service console. For more information, see [View job logs on Log Service](/intl.en-US/Fully Managed Flink/O&M management/View job logs.md).


## Specify that all the job logs in a workspace are exported to Log Service

You can configure **Deployment Defaults** to specify that all the job logs in a workspace are exported to Log Service by default.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, find the workspace that you want to manage, and click **Console** in the **Actions** column.

3.  Configure job logs.

    1.  In the **Logging** section, set **Logging Profile** to **Custom Template**.

    2.  Paste the following content into the code editor of **Custom Template** and specify the parameters in the following table based on your Log Service information.

        -   Per-Job Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="5 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="1"/> 
                </Appender>  
                <Appender name="SLS" type="SLS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
            
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="project">YOUR-SLS-PROJECT</Property>  
                  <Property name="logStore">YOUR-SLS-LOGSTORE</Property> 
                  <Property name="endpoint">YOUR-SLS-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-SLS-ACCESSKEYID</Property> 
                  <Property name="accessKeySecret">YOUR-SLS-ACCESSKEYSECRET</Property> 
                  <Property name="topic">{{ namespace }}:{{ deploymentId }}:{{ jobId }}</Property> 
                  <Property name="flushIntervalSeconds">10</Property>
                  <Property name="flushIntervalEventCount">100</Property>
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="SLS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        -   Session Clusters mode

            ```
            <?xml version="1.0" encoding="UTF-8"?>
            <Configuration xmlns="http://logging.apache.org/log4j/2.0/config" 
            strict="true" packages="com.ververica.platform.logging.appender" status="WARN">  
              <Appenders> 
                <Appender name="StdOut" type="Console"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/> 
                </Appender>  
                <Appender name="RollingFile" type="RollingFile" fileName="${sys:log.file}" filePattern="${sys:log.file}.%i"> 
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
                  <Policies> 
                    <SizeBasedTriggeringPolicy size="5 MB"/> 
                  </Policies>  
                  <DefaultRolloverStrategy max="1"/> 
                </Appender>  
                <Appender name="SLS" type="SLS">
                  <Layout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS}{GMT+8} %-5p %-60c %x - %m%n" type="PatternLayout"/>  
            
                  <!-- The final effective log path is: ${baseUri}/logs/${namespace}/${deploymentId}/{jobId}/ -->
                  <Property name="namespace">{{ namespace }}</Property> <!-- Do not modify this line -->
                  <Property name="project">YOUR-SLS-PROJECT</Property>  
                  <Property name="logStore">YOUR-SLS-LOGSTORE</Property> 
                  <Property name="endpoint">YOUR-SLS-ENDPOINT</Property> 
                  <Property name="accessKeyId">YOUR-SLS-ACCESSKEYID</Property> 
                  <Property name="accessKeySecret">YOUR-SLS-ACCESSKEYSECRET</Property> 
                  <Property name="topic">{{ namespace }}:{{ sessionClusterName }}:{{ sessionClusterId }}</Property> 
                  <Property name="flushIntervalSeconds">10</Property>
                  <Property name="flushIntervalEventCount">100</Property>
                  <Property name="deploymentId">{{ sessionClusterName }}</Property>
                  <Property name="jobId">{{ sessionClusterId }}</Property>
                </Appender>
              </Appenders>  
              <Loggers> 
                <Logger level="INFO" name="org.apache.hadoop"/>  
                <Logger level="INFO" name="org.apache.kafka"/>  
                <Logger level="INFO" name="org.apache.zookeeper"/>  
                <Logger level="INFO" name="akka"/>  
                <Logger level="ERROR" name="org.jboss.netty.channel.DefaultChannelPipeline"/>  
                <Logger level="OFF" name="org.apache.flink.runtime.rest.handler.job.JobDetailsHandler"/> 
                <Logger level="ERROR" name="org.apache.flink.fs.osshadoop.shaded.com.aliyun.oss"/>
                {%- for name, level in userConfiguredLoggers -%} 
                  <Logger level="{{ level }}" name="{{ name }}"/> 
                {%- endfor -%}
                <Root level="{{ rootLoggerLogLevel }}"> 
                  <AppenderRef ref="StdOut"/>
                  <AppenderRef ref="RollingFile"/>  
                  <AppenderRef ref="SLS"/> 
                </Root>
              </Loggers> 
            </Configuration>
            ```

        |Parameter|Description|
        |---------|-----------|
        |YOUR-SLS-PROJECT|Replace the value of this parameter with the project name of your Log Service.|
        |YOUR-SLS-LOGSTORE|Replace the value of this parameter with the Logstore name of your Log Service.|
        |YOUR-SLS-ENDPOINT|Replace the value of this parameter with the endpoint of the region where your Log Service resides. For more information, see [Endpoints](/intl.en-US/Developer Guide/API Reference/Endpoints.md).|
        |YOUR-SLS-ACCESSKEYID|Replace the value of this parameter with the **AccessKey ID** of the account that you use to configure Log Service. **Note:** If you configure Log Service within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure Log Service within a different account from that of a fully managed cluster, this parameter is required. |
        |YOUR-SLS-ACCESSKEYSECRET|Replace the value of this parameter with the **AccessKey secret** of the account that you use to configure Log Service. **Note:** If you configure Log Service within the same account as that of a fully managed cluster, you can leave this parameter blank. If you leave this parameter blank, you must delete this parameter. If you configure Log Service within a different account from that of a fully managed cluster, this parameter is required. |
        |sessionClusterName|Replace the value of this parameter with the name of a session cluster.|
        |sessionClusterId|Replace the value of this parameter with the ID of a session cluster.|

        **Note:**

        -   Make sure that the **AccessKey ID** and **AccessKey secret** are the key pair managed within the account that you use to activate **fully managed Flink**.
        -   Make sure that the account you use to activate **fully managed Flink** has the permissions to read and write the project of Log Service.
4.  Click **Save**.

    **Note:** After you configure **Deployment Defaults**, the logs of all the jobs that are created in the workspace are stored in the Logstore of the Log Service project that you configure. You can view the logs of the job in the Log Service console. For more information, see [View job logs on Log Service](/intl.en-US/Fully Managed Flink/O&M management/View job logs.md).


