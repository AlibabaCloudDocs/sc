# CLUSTER\_SERVING

本文以输入格式为input\_shape=\(?,128\)的Tensorflow SavedMode模型为例，为您介绍如何使用机器学习函数CLUSTER\_SERVING进行在线推理。

## 使用限制

本文仅适用于VVP 2.4.2及以上版本。

## 背景信息

Analytics Zoo是英特尔®开源的统一大数据分析和AI平台，支持分布式TensorFlow及PyTorch的训练和推理，通过英特尔® OpenVINO工具套件和DL Boost指令集等，提升深度学习工作负载的性能。Analytics Zoo Cluster Serving是Analytics Zoo的分布式推理解决方案，可以部署在Apache Flink集群上进行分布式运算。通过使用Cluster Serving，您无需编写任何机器学习相关代码，即可实现端到端的分布式机器学习推理流程。Analytics Zoo详情请参见[Analytics Zoo](https://analytics-zoo.github.io/master/#ClusterServingGuide/ProgrammingGuide/)。

Cluster Serving将其部分功能集成在Flink的内置函数中，支持TensorFlow、PyTorch和OpenVINO的推理模型。您可以直接在Flink开发控制台中调用CLUSTER\_SERVING函数，进行分布式机器学习推理。使用CLUSTER\_SERVING函数时，您只需要提供模型文件和数据文件，即可完成机器学习端到端的应用。

## 语法

```
CLUSTER_SERVING(uri, `data`)
```

## 入参

|参数|数据类型|说明|
|--|----|--|
|uri|String|数据ID。|
|data|String|数据。|

## 示例

-   测试数据

    |uri\(String\)|data\(String\)|
    |-------------|--------------|
    |my-data|0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0 0.0|

-   测试步骤
    1.  登录OSS控制台，上传测试数据input.csv文件至oss://\*\*\*/input.csv目录。
    2.  在Flink开发控制台，创建源表和结果表、加载模型并将CLUSTER\_SERVING函数在线推理的数据插入结果表。

        ```
        CREATE TEMPORARY TABLE ClusterServingInput(
           uri STRING,
          `data` STRING
        )  WITH  (
           'connector' = 'filesystem',
           'path' = 'oss://alink-vvp/artifacts/namespaces/test-module-default/input.csv',
           'format' = 'csv'
        );
        
        CREATE TEMPORARY TABLE ClusterServingOutput (
           uri1 STRING,  
           data1 STRING
         ) WITH (
           'connector' = 'print'
        );
        
        LOAD MODULE `cluster-serving`;
        INSERT INTO ClusterServingOutput SELECT cluster_serving(uri, `data`) FROM ClusterServingInput;
        ```

    3.  在作业**高级配置**的**更多Flink配置**和Preview Session集群**其他配置**均增加以下配置。

        ```
        pipeline.global-job-parameters: '"modelPath:""[模型目录地址]"""'
        ```

        其中，模型目录地址为您模型文件的OSS存放目录，例如oss://\*\*\*/tf\_auto/。

    4.  在作业运维界面，单击目标作业**操作**列下的**运行**。
-   测试结果

    |uri1|data1|
    |----|-----|
    |0.03932024,0.017788976,-2.2538425E-4,0.021853039,0.030905273,0.052324444,0.053021688,0.08776946,0.0625467,0.12149326,0.048770625,0.04533424,0.09271586,0.023119276,0.0037812046,0.052707616,0.029312208,0.023519568,0.07501668,0.037939064,0.06363185,0.04994791,0.08504477,0.06940596,0.052632406,0.028651983,0.02205597,0.05861686,0.040175162,0.04682718,0.05283319,0.01967535,0.111778766,0.03182444,0.04509241,0.09942829,0.009491239,0.038903005,0.11488753,0.019503327,0.0346709,0.060727723,0.03718308,0.1295206,0.04292037,0.13272661,0.009957914,0.050370887,0.03016096,0.067269124,0.09981682,0.08606887,0.085193545,0.041775055,0.02956512,0.027142545,0.09836763,0.029330501,0.040107135,0.0753366,0.068517864,0.020111244,0.086271614,0.04449262,0.107297346,0.07932708,0.036529146,0.014892701,0.08245985,0.04272034,0.076421194,0.035283472,0.028554386,0.019374546,0.048012834,0.011391987,0.028220229,0.054405782,0.037807927,0.085761145,0.07974813,0.04150249,-0.017132144,0.08219319,0.019887058,0.034193676,0.06221051,0.07516215,0.0598385,0.09159884,0.038117558,0.05329266,0.021038251,0.072046585,0.05561088,0.017538168,0.025691401,0.08580602,0.02225845,0.117060736,0.060743354,0.11840888,0.065381594,0.08059649,0.045638822,0.06399781,0.011646094,0.043574452,0.123603456,0.045708194,0.05672055,0.08402837,0.118181005,0.09657015,0.08259893,0.0010145458,0.031954847,0.07388758,0.035380267,0.070080444,0.06764534,0.058731187,0.022044828,0.094359346,0.05559277,0.032255664,0.07360537,0.11992536|128|


