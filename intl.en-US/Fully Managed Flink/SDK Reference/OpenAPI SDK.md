# OpenAPI SDK

You can use Alibaba Cloud SDK for Java to access fully managed Flink without the need to perform complex programming. This topic describes how to install and use Alibaba Cloud SDK for Java to access fully managed Flink.

## Prerequisites

-   An AccessKey pair is created for a Resource Access Management \(RAM\) user.

    **Note:** We recommend that you create a RAM user and grant the RAM user the permissions to access fully managed Flink. Then, you can use the AccessKey pair of the RAM user to call Alibaba Cloud SDK for Java. This allows you to protect the AccessKey pair of your Alibaba Cloud account. For more information, see the following topics:

    -   For more information about how to create a RAM user, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).
    -   For more information about how to grant the permissions to access fully managed Flink, see [Manual authorization \(method 1\)](/intl.en-US/Fully Managed Flink/Preparations/Grant permissions to a role.md).
    -   For more information about how to create an AccessKey pair for a RAM user, see [Create an AccessKey pair]().
-   Java is installed. Java Development Kit \(JDK\) 8 or later is required for Alibaba Cloud SDK for Java.

## Install Alibaba Cloud SDK for Java

1.  Configure the Maven project management tool in IntelliJ IDEA by using one of the following methods:
    -   Use the integrated Maven project management tool in IntelliJ IDEA.
    -   Download Maven software that corresponds to your operating system from the official Maven website \([Download Apache Maven](http://maven.apache.org/download.cgi)\) and manually configure the Maven tool.
2.  Create a Maven project by using one of the following methods:
    -   Method 1: Create a Maven project in IntelliJ IDEA.

        ![maven1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7942674161/p101344.png)

    -   Method 2: Convert an existing project to a Maven project.
        1.  Right-click the project that you want to convert and select **Add Framework Support...**.

            ![maven2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8942674161/p101351.png)

        2.  Select **Maven** and click **OK**.

            ![maven3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8942674161/p101353.png)

3.  In the pom.xml file under the project directory, add aliyun-java-sdk-core, and add the aliyun-java-sdk-ververica and ververica-common dependencies of fully managed Flink.

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

    After the dependencies are added, the Maven project management tool automatically downloads the relevant JAR packages. You can view the imported dependencies in External Libraries under the project directory.


## Procedure

1.  Specify the regionId parameter of the service and configure the AccessKey and Secret parameters to generate a Client object.

    ```
    /*
      The following four regions are supported:
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

2.  Initialize the request class and use a setter method to configure the request parameters.

    A GetGlobalDeploymentDefaults request is used as an example.

    ```
    GetGlobalDeploymentDefaultsRequest getGlobalDeploymentDefaultsRequest = new GetGlobalDeploymentDefaultsRequest();
    // You can obtain the workspace ID (workspaceId) from the workspace details in the development console of fully managed Flink. 
    // Do not set workspaceId to the name of the workspace. 
    String workspaceId = "<workspaceId>";
    String namespace = "<namespace>";
    getGlobalDeploymentDefaultsRequest.setWorkspace(workspaceId);
    getGlobalDeploymentDefaultsRequest.setNamespace(namespace);
    ```

3.  Call the SDK and view the returned results.
    -   Method 1: Directly display all the returned results.

        ```
        System.out.println(client.doAction(getGlobalDeploymentDefaultsRequest).getHttpContentString());
        ```

    -   Method 2: Deserialize the returned data into the relevant Response object.

        ```
        ResultModel<GetGlobalDeploymentDefaultsResp> globalDefaults = JsonUtil.toBean(client.doAction(getGlobalDeploymentDefaultsRequest).getHttpContentString(), new TypeReference<ResultModel<GetGlobalDeploymentDefaultsResp>>() {
            });
        ```


## Example

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

## Full SDK demo

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
     * Valid format:
     * {
     * "requestId": "202008030937-4TFJR5MK2R",
     * "success": true, # If success is set to true, only the request ID is displayed. If success is set to false, the reason and message are displayed.
     * "message": "",
     * "reason": "",
     * "data": Data content (object)
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
         The following four regions are supported:
         ververica.cn-hangzhou.aliyuncs.com
         ververica.cn-shanghai.aliyuncs.com
         ververica.cn-shenzhen.aliyuncs.com
         ververica.cn-beijing.aliyuncs.com

         */

        String artifactFilename = "test-1.jar";// You must manually upload the Flink DataStream JAR package and specify the name of the artifact that you want to query or delete.


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
               If no namespaces are displayed, go to https://realtime-compute.console.aliyun.com/#/dashboard/serverless/asi to create a namespace.
             */
            System.exit(1);
        }

        // Check the SQL syntax. If the check succeeds, VALIDATION_RESULT_VALID_INSERT_QUERY or VALIDATION_RESULT_VALID_DDL_STATEMENT is returned. Otherwise, view errorDetails.
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


        // Perform a data definition language (DDL) operation. If the operation succeeds, data.result='RESULT_SUCCESS|RESULT_SUCCESS_WITH_ROWS' is returned. Otherwise, view the message. 
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


        // Create a deployment for an SQL job.
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

        // Generate an SQL script.
        Artifact.SqlScriptArtifact sqlScriptArtifact = new Artifact.SqlScriptArtifact();
        sqlScriptArtifact.setSqlScript("INSERT INTO blackhole_sink SELECT UPPER(name), score FROM datagen_source");
        spec.getTemplate().getSpec().setArtifact(sqlScriptArtifact);

        // Obtain the list of artifacts.
        ListArtifactsRequest listArtifactsRequest = new ListArtifactsRequest();
        listArtifactsRequest.setWorkspace(workspaceId);
        listArtifactsRequest.setNamespace(namespace);
        ResultModel<ListArtifactsResp> artifacts = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listArtifactsRequest), new TypeReference<ResultModel<ListArtifactsResp>>() {
        });
        System.out.println(JsonUtil.toJson(artifacts));

        // Obtain an artifact and specify the file name, which will be used later when you create a DataStream job.
        GetArtifactMetadataRequest getArtifactMetadataRequest = new GetArtifactMetadataRequest();
        getArtifactMetadataRequest.setWorkspace(workspaceId);
        getArtifactMetadataRequest.setNamespace(namespace);
        getArtifactMetadataRequest.setFilename(artifactFilename);
        dataStr = SdkUtil.getHttpContentString(client, getArtifactMetadataRequest);
        System.out.println(dataStr);
        ResultModel<GetArtifactMetadataResp> artifact = JsonUtil.toBean(dataStr, new TypeReference<ResultModel<GetArtifactMetadataResp>>() {
        });
        System.out.println(JsonUtil.toJson(artifact));

        // To create a DataStream job, you need only to import different artifacts.
        Artifact.JarArtifact jarArtifact = new Artifact.JarArtifact();
        jarArtifact.setJarUri(artifact.getData().getArtifact().getUri());


        // Obtain the list of clusters on which jobs are deployed.
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

        // Obtain the job information.
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


        // Update the deployment status.
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


        // Create savepoints: If the data field is empty, the request fails to be triggered. Check the message.
        /*
         * If the request succeeds, the data field contains response data: {"data":{"metadata":{"createdAt":"2020-08-18T07:04:40.379926Z","jobId":"d0d6720f-00ac-47cc-8d54-7d88a4d7b446","modifiedAt":"2020-08-18T07:04:40.379926Z","deploymentId":"0cb796fc-2641-488e-bde8-52b2199c5747","origin":"USER_REQUEST","resourceVersion":1,"namespace":"test","annotations":{"com.dataartisans.appmanager.controller.deployment.spec.version":"4"},"id":"c4e40dc9-69c7-4ba0-8add-5f862ecb00ef"},"apiVersion":"v1","kind":"Savepoint","spec":{"savepointLocation":"oss://qiqi-zp/vvp/flink-savepoints/namespaces/test/deployments/0cb796fc-2641-488e-bde8-52b2199c5747/c4e40dc9-69c7-4ba0-8add-5f862ecb00ef"},"status":{"state":"STARTED"}},"requestId":"188B1FFC-2729-458E-A404-D3CD5D972DA0"}
         * If the request fails, no data field is displayed and a message is returned: {"RequestId":"B545121D-ED4C-4E71-9D76-CBD510A04E5B","HostId":"ververica-share.cn-shanghai.aliyuncs.com","Code":"BadRequest","Message":"No active job for a deployment."}
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


        // Obtain the list of savepoints.
        ListSavepointsRequest listSavepointsRequest = new ListSavepointsRequest();
        listSavepointsRequest.setWorkspace(workspaceId);
        listSavepointsRequest.setNamespace(namespace);
        listSavepointsRequest.setDeploymentId(deploymentId);
        ResultModel<ListSavepointsResp> savepoints = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listSavepointsRequest), new TypeReference<ResultModel<ListSavepointsResp>>() {
        });
        System.out.println(JsonUtil.toJson(savepoints.getData().getSavepoints()));
        System.out.println(JsonUtil.toJson(savepoints.getData().getSavepoints().size()));


        // Obtain the list of jobs.
        ListJobsRequest listJobsRequest = new ListJobsRequest();
        listJobsRequest.setWorkspace(workspaceId);
        listJobsRequest.setNamespace(namespace);
        listJobsRequest.setDeploymentId(deploymentId);
        ResultModel<ListJobsResp> jobs = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listJobsRequest), new TypeReference<ResultModel<ListJobsResp>>() {
        });
        System.out.println(JsonUtil.toJson(jobs.getData().getJobs()));


        // Delete an artifact.
        DeleteArtifactRequest deleteArtifactRequest = new DeleteArtifactRequest();
        deleteArtifactRequest.setWorkspace(workspaceId);
        deleteArtifactRequest.setNamespace(namespace);
        deleteArtifactRequest.setFilename(artifactFilename);
        DeleteArtifactResponse deleteArtifactResponse = client.getAcsResponse(deleteArtifactRequest);
        System.out.println(JsonUtil.toJson(deleteArtifactResponse));


        // Obtain the list of deployments.
        ListDeploymentsRequest listDeploymentsRequest = new ListDeploymentsRequest();
        listDeploymentsRequest.setWorkspace(workspaceId);
        listDeploymentsRequest.setNamespace(namespace);
        ResultModel<ListDeploymentsResp> deployments = JsonUtil.toBean(SdkUtil.getHttpContentString(client, listDeploymentsRequest), new TypeReference<ResultModel<ListDeploymentsResp>>() {
        });
        System.out.println(JsonUtil.toJson(deployments));


        // Specify DeploymentId to delete an existing deployment that is in the CANCELLED state.
        DeleteDeploymentRequest deleteDeploymentRequest = new DeleteDeploymentRequest();
        deleteDeploymentRequest.setWorkspace(workspaceId);
        deleteDeploymentRequest.setNamespace(namespace);
        deleteDeploymentRequest.setDeploymentId(deploymentId);
        DeleteDeploymentResponse deleteDeploymentResponse = client.getAcsResponse(deleteDeploymentRequest);
        System.out.println(JsonUtil.toJson(deleteDeploymentResponse));

        // Obtain the job template.
        GetDeploymentDefaultsRequest getDeploymentDefaultsRequest = new GetDeploymentDefaultsRequest();
        getDeploymentDefaultsRequest.setWorkspace(workspaceId);
        getDeploymentDefaultsRequest.setNamespace(namespace);
        ResultModel<GetDeploymentDefaultsResp> deploymentDefaults = JsonUtil.toBean(client.doAction(getDeploymentDefaultsRequest).getHttpContentString(), new TypeReference<ResultModel<GetDeploymentDefaultsResp>>() {
        });
        System.out.println(JsonUtil.toJson(deploymentDefaults.getData()));

        // Update the job information.
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

