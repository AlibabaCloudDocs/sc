# OpenAPI SDK

您可以使用阿里云开发者工具套件（Alibaba Cloud SDK for Java），不用复杂编程即可访问Flink全托管服务。本文为您介绍如何安装并使用阿里云开发者工具套件访问Flink全托管服务。

## 前提条件

-   RAM用户已创建AccessKey。

    **说明：** 为避免主账号泄露AccessKey带来安全风险，建议您创建RAM用户，授予RAM用户Flink全托管相关的访问权限，再使用RAM用户的AccessKey调用SDK。相关文档请参见：

    -   创建RAM用户操作步骤，请参见[创建RAM用户](/intl.zh-CN/用户管理/基本操作/创建RAM用户.md)。
    -   Flink全托管相关访问权限，请参见[手动授权（方式一）](/intl.zh-CN/Flink全托管/准备工作/角色授权.md)。
    -   为RAM用户创建AccessKey，请参见[创建AccessKey]()。
-   已安装Java环境。Alibaba Cloud SDK for Java要求使用JDK 8或更高版本。

## 安装Alibaba Cloud SDK for Java

1.  通过以下任一方式在IDEA中配置Maven项目管理工具。
    -   使用IDEA中集成的Maven项目管理工具。
    -   访问Maven官方下载页面（[Download Apache Maven](http://maven.apache.org/download.cgi)）下载对应操作系统的Maven工具，手动配置Maven工具。
2.  通过以下任一方式创建Maven项目。
    -   方式一：在IDEA中添加一个Maven项目。

        ![maven1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9037428951/p101344.png)

    -   方式二：将已有的项目转换为Maven项目。
        1.  右键单击要转换的项目，并选择**Add Framework Support...**。

            ![maven2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0137428951/p101351.png)

        2.  选择**Maven**，并单击**OK**。

            ![maven3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0137428951/p101353.png)

3.  在项目目录下的pom.xml文件中，添加阿里云的aliyun-java-sdk-core后，还需要添加Flink全托管的aliyun-java-sdk-ververica和ververica-common依赖。

    ```
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-sdk-core</artifactId>
        <version>4.5.3</version>
    </dependency>
    
    <dependency>
      <groupId>com.aliyun</groupId>
      <artifactId>aliyun-java-sdk-ververica</artifactId>
      <version>1.0.0</version>
    </dependency>
    
    <dependency>
      <groupId>com.aliyun</groupId>
      <artifactId>ververica-common</artifactId>
      <version>1.0.0</version>
    </dependency>
    ```

    添加依赖后，Maven项目管理工具会自动下载相关JAR包，您可以在项目目录下的External Libraries查看已成功导入的依赖。


## 请求步骤

1.  配置服务所在的regionId，用户的AccessKey和Secret，来生成Client对象。

    ```
    /*
      目前支持以下4个region
      cn-hangzhou
      cn-shanghai
      cn-shenzhen
      cn-beijing
    */
    String regionId = "<regionId>";
    String AccessKey = "<AccessKey>";
    String Secret = "<Secret>";
    IClientProfile profile = DefaultProfile.getProfile(regionId, AccessKey, Secret);
    DefaultAcsClient client = new DefaultAcsClient(profile);
    ```

2.  初始化请求类，使用setter设置请求参数。

    以GetGlobalDeploymentDefaults请求为例。

    ```
    GetGlobalDeploymentDefaultsRequest getGlobalDeploymentDefaultsRequest = new GetGlobalDeploymentDefaultsRequest();
    // 您可以在Flink开发控制台，工作空间详情中获取工作空间ID（workspaceId）。
    // 请不要在workspaceId中直接写入工作空间名称。
    String workspaceId = "<workspaceId>";
    String namespace = "<namespace>";
    getGlobalDeploymentDefaultsRequest.setWorkspace(workspaceId);
    getGlobalDeploymentDefaultsRequest.setNamespace(namespace);
    ```

3.  调用并返回结果。
    -   方式一：直接print全部返回结果。

        ```
        System.out.println(client.doAction(getGlobalDeploymentDefaultsRequest).getHttpContentString());
        ```

    -   方式二：反序列化成相应的Response对象。

        ```
        ResultModel<GetGlobalDeploymentDefaultsResp> globalDefaults = JsonUtil.toBean(client.doAction(getGlobalDeploymentDefaultsRequest).getHttpContentString(), new TypeReference<ResultModel<GetGlobalDeploymentDefaultsResp>>() {
            });
        ```


## 参考示例

```
String regionId = "cn-hangzhou";

IClientProfile profile = DefaultProfile.getProfile(regionId, "AccessKey", "Secret");
DefaultAcsClient client = new DefaultAcsClient(profile);

GetGlobalDeploymentDefaultsRequest getGlobalDeploymentDefaultsRequest = new GetGlobalDeploymentDefaultsRequest();
getGlobalDeploymentDefaultsRequest.setWorkspace(workspaceId);
getGlobalDeploymentDefaultsRequest.setNamespace(namespace);

ResultModel<GetGlobalDeploymentDefaultsResp> globalDefaults = JsonUtil.toBean(client.doAction(getGlobalDeploymentDefaultsRequest).getHttpContentString(), new TypeReference<ResultModel<GetGlobalDeploymentDefaultsResp>>() {
    });
```

## 全部SDK Demo

```
package sample;

import com.aliyuncs.DefaultAcsClient;
import com.aliyuncs.exceptions.ClientException;
import com.aliyuncs.http.FormatType;
import com.aliyuncs.profile.DefaultProfile;
import com.aliyuncs.profile.IClientProfile;
import com.aliyuncs.ververica.model.v20200501.*;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.core.type.TypeReference;
import com.ververica.common.model.ResultModel;
import com.ververica.common.model.deployment.Artifact;
import com.ververica.common.model.deployment.Deployment;
import com.ververica.common.model.deployment.DeploymentState;
import com.ververica.common.model.namespace.Namespace;
import com.ververica.common.params.*;
import com.ververica.common.resp.*;
import com.ververica.common.util.JsonUtil;
import com.ververica.common.util.SdkUtil;

import java.util.List;


public class SdkSample {

    /**
     * 正常格式：
     * {
     * "requestId": "202008030937-4TFJR5MK2R",
     * "success": true, #为true时，结果中仅展示requestId，false时会抛出reason和message
     * "message": "",
     * "reason": "",
     * "data": 数据内容（Object）
     * }
     */


    public static void main(String[] args) throws JsonProcessingException, ClientException {

        final String AccessKey = "<AccessKey>";
        final String Secret = "<Secret>";
        final String workspaceId = "<workspaceId>";
        final String namespace = "<namespace>";

        String regionId = "cn-hangzhou";

        IClientProfile profile = DefaultProfile.getProfile(regionId, AccessKey, Secret);
        DefaultAcsClient client = new DefaultAcsClient(profile);

        /*
         目前支持以下4个region
         ververica.cn-hangzhou.aliyuncs.com
         ververica.cn-shanghai.aliyuncs.com
         ververica.cn-shenzhen.aliyuncs.com
         ververica.cn-beijing.aliyuncs.com

         */

        String artifactFilename = "test-1.jar";//需要您手动上传flink datastream JAR包，指定获取或删除的artifact名字


        // List namespaces
        ListNamespacesRequest listNamespacesRequest = new ListNamespacesRequest();
        ResultModel<ListNamespacesResp> listNamespacesRespResultModel = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listNamespacesRequest), new TypeReference<ResultModel<ListNamespacesResp>>() {
        });
        List<Namespace> namespaceList = listNamespacesRespResultModel.getData().getNamespaces();
        System.out.println(JsonUtil.toJson(namespaceList));
        String workspaceId = "";
        String namespace = "";

        if (null != namespaceList && namespaceList.size() != 0) {
            workspaceId = namespaceList.get(0).getWorkspace();
            String namespaces = namespaceList.get(0).getName();
            String[] ns = namespaces.split("/");
            namespace = ns[1];
        } else {
            /*
               没有namespace，需要购买https://realtime-compute.console.aliyun.com/#/dashboard/serverless/asi
             */
            System.exit(1);
        }

        //sql语法检查，成功则validationResult：VALIDATION_RESULT_VALID_INSERT_QUERY或者VALIDATION_RESULT_VALID_DDL_STATEMENT，其他查看errorDetails
        ValidateSqlScriptRequest validateSqlScriptRequest = new ValidateSqlScriptRequest();
        validateSqlScriptRequest.setWorkspace(workspaceId);
        validateSqlScriptRequest.setNamespace(namespace);
        ValidateSqlScriptParams validateSqlScriptParams = new ValidateSqlScriptParams();
        validateSqlScriptParams.setStatement("CREATE TABLE datagen_source ( name VARCHAR, score BIGINT ) COMMENT 'datagen source table' WITH ( 'connector' = 'datagen' )");
        validateSqlScriptRequest.setParamsJson(JsonUtil.toJson(validateSqlScriptParams));
        validateSqlScriptRequest.setHttpContentType(FormatType.JSON);
        ResultModel<ValidateSqlScriptResp> validateSqlScriptRespResultModel = JsonUtil.toBean(SdkUtil.getHttpContentString(client, validateSqlScriptRequest), new TypeReference<ResultModel<ValidateSqlScriptResp>>() {
        });
        System.out.println(JsonUtil.toJson(validateSqlScriptRespResultModel));


        // 执行ddl操作：如果成功data.result='RESULT_SUCCESS|RESULT_SUCCESS_WITH_ROWS'，否则查看message。
        ExecuteSqlScriptRequest executeSqlScriptRequest = new ExecuteSqlScriptRequest();
        executeSqlScriptRequest.setWorkspace(workspaceId);
        executeSqlScriptRequest.setNamespace(namespace);
        ExecuteSqlScriptParams executeSqlScriptParams = new ExecuteSqlScriptParams();
        executeSqlScriptParams.setStatement("create table RAN_TABLE (a varchar) with ('connector' = 'random', 'type' = 'random');");
        executeSqlScriptRequest.setParamsJson(JsonUtil.toJson(executeSqlScriptParams));
        executeSqlScriptRequest.setHttpContentType(FormatType.JSON);
        ResultModel<ExecuteSqlScriptResp> executeSqlScriptRespResultModel = JsonUtil.toBean(SdkUtil.getHttpContentString(client, executeSqlScriptRequest), new TypeReference<ResultModel<ExecuteSqlScriptResp>>() {
        });
        System.out.println(JsonUtil.toJson(executeSqlScriptRespResultModel));


        //创建deployment(sql作业)
        GetGlobalDeploymentDefaultsRequest getGlobalDeploymentDefaultsRequest = new GetGlobalDeploymentDefaultsRequest();
        getGlobalDeploymentDefaultsRequest.setWorkspace(workspaceId);
        getGlobalDeploymentDefaultsRequest.setNamespace(namespace);
        String dataStr = SdkUtil.getHttpContentString(client, getGlobalDeploymentDefaultsRequest);
        System.out.println(dataStr);
        ResultModel<GetGlobalDeploymentDefaultsResp> globalDefaults = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<GetGlobalDeploymentDefaultsResp>>() {
        });
        System.out.println(JsonUtil.toJson(globalDefaults.getData()));

        Deployment.DeploymentSpec spec = globalDefaults.getData().getSpec();
        spec.setState(DeploymentState.RUNNING);

        // SQL作业
        Artifact.SqlScriptArtifact sqlScriptArtifact = new Artifact.SqlScriptArtifact();
        sqlScriptArtifact.setSqlScript("INSERT INTO blackhole_sink SELECT UPPER(name), score FROM datagen_source");
        spec.getTemplate().getSpec().setArtifact(sqlScriptArtifact);

        // 获取artifacts列表
        ListArtifactsRequest listArtifactsRequest = new ListArtifactsRequest();
        listArtifactsRequest.setWorkspace(workspaceId);
        listArtifactsRequest.setNamespace(namespace);
        ResultModel<ListArtifactsResp> artifacts = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listArtifactsRequest), new TypeReference<ResultModel<ListArtifactsResp>>() {
        });
        System.out.println(JsonUtil.toJson(artifacts));

        // 获取artifact，指定Filename，后续创建datastream作业时使用
        GetArtifactMetadataRequest getArtifactMetadataRequest = new GetArtifactMetadataRequest();
        getArtifactMetadataRequest.setWorkspace(workspaceId);
        getArtifactMetadataRequest.setNamespace(namespace);
        getArtifactMetadataRequest.setFilename(artifactFilename);
        dataStr = SdkUtil.getHttpContentString(client, getArtifactMetadataRequest);
        System.out.println(dataStr);
        ResultModel<GetArtifactMetadataResp> artifact = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<GetArtifactMetadataResp>>() {
        });
        System.out.println(JsonUtil.toJson(artifact));

        // 创建DataStream作业只需要传入不同的Artifact即可
        Artifact.JarArtifact jarArtifact = new Artifact.JarArtifact();
        jarArtifact.setJarUri(artifact.getData().getArtifact().getUri());


        // 获取部署目标列表
        ListDeploymentTargetsRequest listDeploymentTargetsRequest = new ListDeploymentTargetsRequest();
        listDeploymentTargetsRequest.setWorkspace(workspaceId);
        listDeploymentTargetsRequest.setNamespace(namespace);
        ResultModel<ListDeploymentTargetsResp> deploymentTargets = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listDeploymentTargetsRequest), new TypeReference<ResultModel<ListDeploymentTargetsResp>>() {
        });
        System.out.println(JsonUtil.toJson(deploymentTargets.getData().getDeploymentTargets()));

        CreateDeploymentParams createDeploymentParams = new CreateDeploymentParams();
        Deployment.DeploymentMetadata metadata = new Deployment.DeploymentMetadata();
        metadata.setName("sql-test-1");
        spec.setDeploymentTargetId(deploymentTargets.getData().getDeploymentTargets().get(0).getMetadata().getId());
        createDeploymentParams.setMetadata(metadata);
        createDeploymentParams.setSpec(spec);

        CreateDeploymentRequest createDeploymentRequest = new CreateDeploymentRequest();
        createDeploymentRequest.setWorkspace(workspaceId);
        createDeploymentRequest.setNamespace(namespace);

        String paramsStr = JsonUtil.toJson(createDeploymentParams);
        System.out.printf("##########params:\n%s\n", paramsStr);
        createDeploymentRequest.setParamsJson(paramsStr);
        createDeploymentRequest.setHttpContentType(FormatType.JSON);
        dataStr = SdkUtil.getHttpContentString(client, createDeploymentRequest);
        System.out.println(dataStr);

        ResultModel<CreateDeploymentResp> createDeploymentRespResultModel = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<CreateDeploymentResp>>() {
        });
        System.out.println(JsonUtil.toJson(createDeploymentRespResultModel));
        String deploymentId = createDeploymentRespResultModel.getData().getMetadata().getId();

        // 获取deployment
        GetDeploymentRequest getDeploymentRequest = new GetDeploymentRequest();
        getDeploymentRequest.setWorkspace(workspaceId);
        getDeploymentRequest.setNamespace(namespace);
        getDeploymentRequest.setDeploymentId(deploymentId);
        dataStr = SdkUtil.getHttpContentString(client, getDeploymentRequest);
        System.out.println(dataStr);
        ResultModel<GetDeploymentResp> getDeploymentRespResultModel = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<GetDeploymentResp>>() {
        });
        Deployment deployment = getDeploymentRespResultModel.getData();
        deployment.getMetadata().getAnnotations().put("update-flag", "zdbox");
        deployment.getMetadata().getLabels().put("key2", "value2");
        deployment.getSpec().setState(DeploymentState.RUNNING);


        // 更新deployment状态
        UpdateDeploymentDesiredStateParams updateDeploymentDesiredStateParams = new UpdateDeploymentDesiredStateParams();
        updateDeploymentDesiredStateParams.setState(DeploymentState.CANCELLED);

        UpdateDeploymentDesiredStateRequest updateDeploymentDesiredStateRequest = new UpdateDeploymentDesiredStateRequest();
        updateDeploymentDesiredStateRequest.setWorkspace(workspaceId);
        updateDeploymentDesiredStateRequest.setNamespace(namespace);
        updateDeploymentDesiredStateRequest.setDeploymentId(deploymentId);
        updateDeploymentDesiredStateRequest.setParamsJson(JsonUtil.toJson(updateDeploymentDesiredStateParams));
        updateDeploymentDesiredStateRequest.setHttpContentType(FormatType.JSON);

        dataStr = SdkUtil.getHttpContentString(client, updateDeploymentDesiredStateRequest);
        System.out.println(dataStr);
        ResultModel<UpdateDeploymentDesiredStateResp> updateDeploymentDesiredStateRespResultModel = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<UpdateDeploymentDesiredStateResp>>() {
        });
        System.out.println(JsonUtil.toJson(updateDeploymentDesiredStateRespResultModel));


        // Create savepoint：如果data为空，则说明触发失败，请查看message
        /*
         * 成功包含data字段返回数据：{"data":{"metadata":{"createdAt":"2020-08-18T07:04:40.379926Z","jobId":"d0d6720f-00ac-47cc-8d54-7d88a4d7b446","modifiedAt":"2020-08-18T07:04:40.379926Z","deploymentId":"0cb796fc-2641-488e-bde8-52b2199c5747","origin":"USER_REQUEST","resourceVersion":1,"namespace":"test","annotations":{"com.dataartisans.appmanager.controller.deployment.spec.version":"4"},"id":"c4e40dc9-69c7-4ba0-8add-5f862ecb00ef"},"apiVersion":"v1","kind":"Savepoint","spec":{"savepointLocation":"oss://qiqi-zp/vvp/flink-savepoints/namespaces/test/deployments/0cb796fc-2641-488e-bde8-52b2199c5747/c4e40dc9-69c7-4ba0-8add-5f862ecb00ef"},"status":{"state":"STARTED"}},"requestId":"188B1FFC-2729-458E-A404-D3CD5D972DA0"}
         * 失败无data字段，并且有message：{"RequestId":"B545121D-ED4C-4E71-9D76-CBD510A04E5B","HostId":"ververica-share.cn-shanghai.aliyuncs.com","Code":"BadRequest","Message":"No active job for a deployment."}
         */

        CreateSavepointRequest createSavepointRequest = new CreateSavepointRequest();
        createSavepointRequest.setWorkspace(workspaceId);
        createSavepointRequest.setNamespace(namespace);
        CreateSavepointParams createSavepointParams = new CreateSavepointParams();
        createSavepointParams.setDeploymentId(deploymentId);
        createSavepointRequest.setParamsJson(JsonUtil.toJson(createSavepointParams));
        createSavepointRequest.setHttpContentType(FormatType.JSON);
        dataStr = SdkUtil.getHttpContentString(client, createSavepointRequest);
        System.out.println(dataStr);
        ResultModel<CreateSavepointResp> savepoint = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<CreateSavepointResp>>() {
        });
        System.out.println(JsonUtil.toJson(savepoint));


        // 获取savepoints列表
        ListSavepointsRequest listSavepointsRequest = new ListSavepointsRequest();
        listSavepointsRequest.setWorkspace(workspaceId);
        listSavepointsRequest.setNamespace(namespace);
        listSavepointsRequest.setDeploymentId(deploymentId);
        ResultModel<ListSavepointsResp> savepoints = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listSavepointsRequest), new TypeReference<ResultModel<ListSavepointsResp>>() {
        });
        System.out.println(JsonUtil.toJson(savepoints.getData().getSavepoints()));
        System.out.println(JsonUtil.toJson(savepoints.getData().getSavepoints().size()));


        // 获取jobs列表
        ListJobsRequest listJobsRequest = new ListJobsRequest();
        listJobsRequest.setWorkspace(workspaceId);
        listJobsRequest.setNamespace(namespace);
        listJobsRequest.setDeploymentId(deploymentId);
        ResultModel<ListJobsResp> jobs = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listJobsRequest), new TypeReference<ResultModel<ListJobsResp>>() {
        });
        System.out.println(JsonUtil.toJson(jobs.getData().getJobs()));


        // 删除artifact
        DeleteArtifactRequest deleteArtifactRequest = new DeleteArtifactRequest();
        deleteArtifactRequest.setWorkspace(workspaceId);
        deleteArtifactRequest.setNamespace(namespace);
        deleteArtifactRequest.setFilename(artifactFilename);
        DeleteArtifactResponse deleteArtifactResponse = client.getAcsResponse(deleteArtifactRequest);
        System.out.println(JsonUtil.toJson(deleteArtifactResponse));


        // 获取deployments列表
        ListDeploymentsRequest listDeploymentsRequest = new ListDeploymentsRequest();
        listDeploymentsRequest.setWorkspace(workspaceId);
        listDeploymentsRequest.setNamespace(namespace);
        ResultModel<ListDeploymentsResp> deployments = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listDeploymentsRequest), new TypeReference<ResultModel<ListDeploymentsResp>>() {
        });
        System.out.println(JsonUtil.toJson(deployments));


        // 删除deployment指定已存在的且状态为CANCELLED的DeploymentId
        DeleteDeploymentRequest deleteDeploymentRequest = new DeleteDeploymentRequest();
        deleteDeploymentRequest.setWorkspace(workspaceId);
        deleteDeploymentRequest.setNamespace(namespace);
        deleteDeploymentRequest.setDeploymentId(deploymentId);
        DeleteDeploymentResponse deleteDeploymentResponse = client.getAcsResponse(deleteDeploymentRequest);
        System.out.println(JsonUtil.toJson(deleteDeploymentResponse));

        // 获取作业模板
        GetDeploymentDefaultsRequest getDeploymentDefaultsRequest = new GetDeploymentDefaultsRequest();
        getDeploymentDefaultsRequest.setWorkspace(workspaceId);
        getDeploymentDefaultsRequest.setNamespace(namespace);
        ResultModel<GetDeploymentDefaultsResp> deploymentDefaults = JsonUtil.toBean(client.doAction(getDeploymentDefaultsRequest).getHttpContentString(), new TypeReference<ResultModel<GetDeploymentDefaultsResp>>() {
        });
        System.out.println(JsonUtil.toJson(deploymentDefaults.getData()));

        // 更新作业信息
        UpdateDeploymentParams deployment = new UpdateDeploymentParams();
        Deployment.DeploymentMetadata deploymentMetadata = new Deployment.DeploymentMetadata();
        deploymentMetadata.setNamespace(namespace);
        deploymentMetadata.setName(jobName);
        deployment.setMetadata(deploymentMetadata);
        
        UpdateDeploymentRequest updateDeploymentRequest = new UpdateDeploymentRequest();
        updateDeploymentRequest.setWorkspace(workspaceId);
        updateDeploymentRequest.setNamespace(namespace);
        updateDeploymentRequest.setHttpContentType(FormatType.JSON);
        updateDeploymentRequest.setDeploymentId(depid);
        String deploymentStr = JsonUtil.toJson(deployment);
        updateDeploymentRequest.setParamsJson(deploymentStr);
        System.out.println("updatedeployment request:"+JsonUtil.toJson(updateDeploymentRequest));
        ResultModel<UpdateDeploymentResp> resultModel = JsonUtil.toBean(SdkUtil.getHttpContentString(client, updateDeploymentRequest), new TypeReference<ResultModel<UpdateDeploymentResp>>() {
        });
        System.out.println("updatedeployment response:"+JsonUtil.toJson(resultModel));

    }
}
```

