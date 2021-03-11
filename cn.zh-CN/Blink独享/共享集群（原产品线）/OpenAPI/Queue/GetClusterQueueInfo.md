# GetClusterQueueInfo

您可以通过GetClusterQueueInfo查询集群上已存在的Queue的信息。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=foas&api=GetClusterQueueInfo&type=ROA&version=2018-11-11)

## 请求头

您可以使用公共请求参数和该接口必填参数。公共请求参数请参见OpenAPI\>调用方式\>公共请求参数文档。

## 请求语法

```
GET /api/v2/clusters/[clusterId]/queueinfo HTTP/1.1
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|clusterId|String|Path|是|h6272cj4etgqe7oets4s\*\*\*\*|集群ID。

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|RegionId|String|Header|是|cn-hangzhou|区域ID。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Queues|Array of Queue| |返回Queues详情。 |
|Queue| | | |
|ClusterId|String|h6272cj4etgqe7oets4s\*\*\*\*|集群ID。

 **说明：** 您可以使用[listcluster](~~117251~~)获取集群ID信息。 |
|ExternalInfo|String|\{\\"minGpu\\":0,\\"maxGpu\\":0,\\"name\\":\\"root.cloudpay\_dev\\",\\"minMem\\":24535,\\"usedVCore\\":50,\\"usedGpu\\":0,\\"usedMem\\":2816,\\"minVCore\\":599,\\"maxVCore\\":600,\\"maxMem\\":24576\}|未转换为INTEGER时的原始信息。

 **说明：**

-   仅在`aliyun-java-sdk-foas 2.7.0`及以上版本返回ExternalInfo。
-   Integer类型返回-9999时，表示该数值超过Integer能表示的大小，请从ExternalInfo中取值。 |
|MaxGpu|Integer|0|最大GPU。 |
|MaxMem|Integer|24576|内存最大值（MB）。 |
|MaxVCore|Integer|599|最大Vcore数。 |
|MinGpu|Integer|0|最小GPU。 |
|MinMem|Integer|24535|最小内存数（MB）。 |
|MinVCore|Integer|599|最小Vcore数。 |
|QueueName|String|root.cloudpay\_dev|队列名称。 |
|UsedGpu|Integer|0|已使用GPU。 |
|UsedMem|Integer|2816|已使用内存（MB）。 |
|UsedVCore|Integer|50|已使用Vcore。 |
|RequestId|String|DACD3B42-5977-4293-B5A8-197A33A954BB|请求ID。 |

## 示例

请求示例

```
/api/v2/clusters/h6272cj4etgqe7oets4s****/queueinfo
```

正常返回示例

`XML`格式

```
<RequestId>DACD3B42-5977-4293-B5A8-197A33A954BB</RequestId>
<Queues>
    <Queue>
        <MaxMem>24576</MaxMem>
        <UsedGpu>0</UsedGpu>
        <ExternalInfo>{"minGpu":0,"maxGpu":0,"name":"root.cloudpay_dev","minMem":24535,"usedVCore":50,"usedGpu":0,"usedMem":2816,"minVCore":599,"maxVCore":600,"maxMem":24576}</ExternalInfo>
        <UsedMem>2816</UsedMem>
        <ClusterId>h6272cj4etgqe7oets4s****</ClusterId>
        <MinGpu>0</MinGpu>
        <MaxVCore>600</MaxVCore>
        <MinVCore>599</MinVCore>
        <MaxGpu>0</MaxGpu>
        <MinMem>24535</MinMem>
        <QueueName>root.cloudpay_dev</QueueName>
        <UsedVCore>50</UsedVCore>
    </Queue>
</Queues>
```

`JSON`格式

```
{
	"RequestId": "DACD3B42-5977-4293-B5A8-197A33A954BB",
	"Queues": {
		"Queue": [
			{
				"MaxMem": 24576,
				"UsedGpu": 0,
				"ExternalInfo": "{\"minGpu\":0,\"maxGpu\":0,\"name\":\"root.cloudpay_dev\",\"minMem\":24535,\"usedVCore\":50,\"usedGpu\":0,\"usedMem\":2816,\"minVCore\":599,\"maxVCore\":600,\"maxMem\":24576}",
				"UsedMem": 2816,
				"ClusterId": "h6272cj4etgqe7oets4s****",
				"MinGpu": 0,
				"MaxVCore": 600,
				"MinVCore": 599,
				"MaxGpu": 0,
				"MinMem": 24535,
				"QueueName": "root.cloudpay_dev",
				"UsedVCore": 50
			}
		]
	}
}
```

