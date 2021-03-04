# ActionTrail logs

Flink Serverless is integrated with ActionTrail. You can view and retrieve logs of user behaviors in ActionTrail. You can also use ActionTrail to deliver the logs to Log Service or a specified OSS bucket to meet requirements such as real-time auditing and problem backtracking.

## ActionTrail logs for Flink Serverless operations

|Event name|Description|
|----------|-----------|
|AppendVswitchId|Adds a vSwitch.|
|CheckAsiLeftResource|Checks the volume of remaining resources.|
|CheckNameSpaceName|Checks the name of a namespace.|
|CheckUserVSwitch|Checks a user vSwitch.|
|CheckUserVpc|Checks the VPC parameters.|
|CheckVirtualClusterName|Checks the name of a cluster.|
|CreateNameSpace|Creates a namespace.|
|CreateServiceLinkedRole|Creates a service-linked role.|
|DeleteNameSpace|Deletes a namespace.|
|DescribeAvailableCidr|Queries available CIDR blocks.|
|GetClusterUsedCu|Queries the number of CUs consumed.|
|GetClusterState|Queries the cluster status.|
|GetOrderDetails|Queries the order details.|
|ListUserVpc|Lists the VPCs of a user.|
|ListUserVswitch|Lists the vSwitches of a user.|
|ListSupportedZone|Lists the supported zones.|
|ListOssInfo|Lists OSS information.|
|OssParamsCheck|Checks the OSS parameters.|
|RefundAsiOrder|Releases a cluster.|
|ResourceSpec|Estimates the resources required to run a Flink job.|
|SearchOrdersV2|Queries the orders.|
|UpdateNameSpace|Updates namespace configurations.|

**Note:**

-   Check for sales events \(creation, renewal, and configuration change\) in the region where the resources are located.
-   Check for non-sales events in the China \(Shanghai\) region.

## Flink Serverless log example

The following example shows the logs generated when you add a vSwitch to a Flink Serverless cluster:

```
{
  "ApiVersion": "2019-06-24",
  "RequestId": "83BB0682-9EBA-402F-8359-26B1676A9EE2",
  "EventType": "ApiCall",
  "UserIdentity": {
    "Type": "root-account",
    "InvokedBy": "",
    "AccountId": "1187xxxxxxxx4544",
    "UserName": "root",
    "PrincipalId": "1187xxxxxxxx4544",
    "AccessKeyId": "TMP.3Ker6WKYHexxxxxxpRuQaM",
    "Arn": ""
  },
  "AcsRegion": "cn-hangzhou",
  "EventName": "AppendVSwitchId",
  "IsBlack": false,
  "RequestParameters": {
    "AcsHost": "foasconsole-share.cn-shanghai.aliyuncs.com",
    "Is4Service": false,
    "ZoneId": "cn-beijing-f",
    "RequestId": "83BB0682-9EBA-402F-8359-26B1676A9EE2",
    "AcsProduct": "foasconsole-inner",
    "InstanceId": "sc_flinkserverless_public_cn-xxxxxxxx",
    "VSwitchIds": "[\\\"vsw-xxxxx\\\",\\\"vsw-xxxxx\\\"]",
    "AcceptLanguage": "zh-CN",
    "RegionId": "cn-shanghai",
    "HostId": "foasconsole-share.cn-shanghai.aliyuncs.com"
  },
  "EventSource": "foasconsole-share.cn-shanghai.aliyuncs.com",
  "ServiceName": "RealtimeCompute",
  "EventTime": "2020-12-01T11:54:59.366+0000",
  "ReferencedResources": {},
  "UserAgent": "realtime-compute.console.aliyun.com",
  "EventId": "83BB0682-9EBA-402F-8359-26B1676A9EE2",
  "ResponseElements": {
    "RequestId": "83BB0682-9EBA-402F-8359-26B1676A9EE2",
    "Result": true,
    "ErrCode": "000000"
  },
  "ErrorCode": "",
  "ErrorMessage": "",
  "EventVersion": "",
  "SourceIpAddress": "100.xx.xx.76"
}
```

