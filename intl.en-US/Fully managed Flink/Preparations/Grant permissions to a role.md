---
keyword: [authorization, role authorization, automated authorization, manual authorization, cluster unavailable]
---

# Grant permissions to a role

Before you activate the fully managed Flink service, you must grant the system default role AliyunStreamAsiDefaultRole to your account. This topic describes how to grant permissions to a role by using two methods.

**Note:** Fully managed Flink can call the relevant services, such as Virtual Private Cloud \(VPC\), Elastic Compute Service \(ECS\), Server Load Balancer \(SLB\), and Application Real-Time Monitoring Service \(ARMS\), to start the relevant components of fully managed Flink only after your account is granted with the AliyunStreamAsiDefaultRole role.

## Automated authorization

In most cases, you are required to perform automated authorization when you **purchase** fully managed Flink for the first time.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  Click **Purchase** under **Fully Managed Flink**.

3.  On the **Authorization Request** page, click **Authorize in RAM**.

    ![Authorize](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8098574161/p110414.png)

4.  On the **Cloud Resource Access Authorization** page, click **Confirm Authorization Policy** at the bottom of the page.

    ![AliyunStreamAsiDefaultRole](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8098574161/p110415.png)

    **Note:** By default, the AliyunStreamAsiDefaultRole role is selected.


## Manual authorization \(method 1\)

If the fully managed Flink service becomes unavailable because you accidentally delete the AliyunStreamAsiDefaultRole role or modify the authorization policy, perform the following steps to perform authorization again:

1.  Create a role.

    1.  Log on to the Resource Access Management \(RAM\) console.

        -   Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
        -   Log on to the [RAM console](https://signin.alibabacloud.com/login.htm) as a RAM user.
    2.  In the left-side navigation pane, click **RAM Roles**.

    3.  Click **Create RAM Role** and enter role information.

        **Note:** The role name is AliyunStreamAsiDefaultRole. If the AliyunStreamDefaultRole role already exists, you do not need to repeatedly create the role.

2.  Add an authorization policy.

    1.  Log on to the RAM console.

        -   Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
        -   Log on to the [RAM console](https://signin.alibabacloud.com/login.htm) as a RAM user.
    2.  In the left-side navigation pane, click **RAM Roles**.

    3.  In the **RAM Role Name** column, find the AliyunStreamAsiDefaultRole role.

    4.  Click **Input and Attach**.

    5.  Select **Custom Policy** as the permission type.

    6.  In the **Policy Name** field, enter a policy name.

        You need to add three authorization policies to the AliyunStreamAsiDefaultRole role:

        -   Policy 1: custom policy \(AliyunStreamAsiDefaultRolePolicy0\)

            ```
            {
                "Version": "1",
                "Statement": [
                    {
                        "Action": "oss:ListBuckets",
                        "Resource": "acs:oss:*:*:*",
                        "Effect": "Allow"
                    },
                    {
                        "Action": [
                            "ecs:AssociateEipAddress",
                            "ecs:AttachNetworkInterface",
                            "ecs:AuthorizeSecurityGroup",
                            "ecs:AuthorizeSecurityGroupEgress",
                            "ecs:CreateNetworkInterface",
                            "ecs:CreateNetworkInterfacePermission",
                            "ecs:CreateSecurityGroup",
                            "ecs:DeleteNetworkInterface",
                            "ecs:DeleteNetworkInterfacePermission",
                            "ecs:DeleteSecurityGroup",
                            "ecs:DescribeNetworkInterfacePermissions",
                            "ecs:DescribeNetworkInterfaces",
                            "ecs:DescribeSecurityGroupAttribute",
                            "ecs:DescribeSecurityGroupReferences",
                            "ecs:DescribeSecurityGroups",
                            "ecs:DetachNetworkInterface",
                            "ecs:JoinSecurityGroup",
                            "ecs:LeaveSecurityGroup",
                            "ecs:ModifyNetworkInterfaceAttribute",
                            "ecs:ModifySecurityGroupAttribute",
                            "ecs:ModifySecurityGroupPolicy",
                            "ecs:ModifySecurityGroupPolicy",
                            "ecs:ModifySecurityGroupRule",
                            "ecs:RevokeSecurityGroup",
                            "ecs:RevokeSecurityGroupEgress",
                            "ecs:UnassociateEipAddress"
                        ],
                        "Resource": "*",
                        "Effect": "Allow"
                    },
                    {
                        "Action": [
                            "arms:ListDashboards"
                        ],
                        "Resource": "*",
                        "Effect": "Allow"
                    },
                    {
                        "Action": [
                            "vpc:DescribeVpcAttribute",
                            "vpc:DescribeVpcs",
                            "vpc:DescribeVSwitchAttributes",
                            "vpc:DescribeVSwitches",
                            "vpc:DescribeRouteTableList",
                            "vpc:DescribeRouteTables",
                            "vpc:DescribeRouteEntryList",
                            "vpc:DescribeRouterInterfaceAttribute",
                            "vpc:DescribeRouterInterfaces",
                            "vpc:DescribeVRouters"
                        ],
                        "Resource": "*",
                        "Effect": "Allow"
                    }
                ]
            }
            ```

        -   Policy 2: custom policy \(AliyunStreamAsiDefaultRolePolicy1\)

            ```
            {
                "Version": "1",
                "Statement": [
                    {
                        "Action": [
                            "slb:AddBackendServers",
                            "slb:AddListenerWhiteListItem",
                            "slb:AddTags",
                            "slb:AddVServerGroupBackendServers",
                            "slb:CreateLoadBalancer",
                            "slb:CreateLoadBalancerHTTPListener",
                            "slb:CreateLoadBalancerHTTPSListener",
                            "slb:CreateLoadBalancerTCPListener",
                            "slb:CreateLoadBalancerUDPListener",
                            "slb:CreateRules",
                            "slb:CreateVServerGroup",
                            "slb:DeleteLoadBalancer",
                            "slb:DeleteLoadBalancerListener",
                            "slb:DeleteRules",
                            "slb:DeleteServerCertificate",
                            "slb:DeleteVServerGroup",
                            "slb:DescribeHealthStatus",
                            "slb:DescribeListenerAccessControlAttribute",
                            "slb:DescribeLoadBalancerAttribute",
                            "slb:DescribeLoadBalancerHTTPListenerAttribute",
                            "slb:DescribeLoadBalancerHTTPListenerAttributes",
                            "slb:DescribeLoadBalancerHTTPSListenerAttribute",
                            "slb:DescribeLoadBalancerTCPListenerAttribute",
                            "slb:DescribeLoadBalancerUDPListenerAttribute",
                            "slb:DescribeLoadBalancers",
                            "slb:DescribeRegions",
                            "slb:DescribeRules",
                            "slb:DescribeServerCertificates",
                            "slb:DescribeTags",
                            "slb:DescribeVServerGroupAttribute",
                            "slb:DescribeVServerGroups",
                            "slb:ModifyLoadBalancerInstanceSpec",
                            "slb:ModifyLoadBalancerInternetSpec",
                            "slb:ModifyLoadBalancerPayType",
                            "slb:RemoveBackendServers",
                            "slb:RemoveListenerWhiteListItem",
                            "slb:RemoveVServerGroupBackendServers",
                            "slb:SetBackendServers",
                            "slb:SetListenerAccessControlStatus",
                            "slb:SetLoadBalancerHTTPListenerAttribute",
                            "slb:SetLoadBalancerHTTPSListenerAttribute",
                            "slb:SetLoadBalancerName",
                            "slb:SetLoadBalancerStatus",
                            "slb:SetLoadBalancerTCPListenerAttribute",
                            "slb:SetLoadBalancerUDPListenerAttribute",
                            "slb:SetRule",
                            "slb:SetServerCertificateName",
                            "slb:SetVServerGroupAttribute",
                            "slb:StartLoadBalancerListener",
                            "slb:StopLoadBalancerListener",
                            "slb:UploadServerCertificate"
                        ],
                        "Resource": "*",
                        "Effect": "Allow"
                    }
                ]
            }
            ```

        -   Policy 3: custom policy \(FlinkServerlessPolicy\)

            ```
            {
                "Version": "1",
                "Statement": [
                    {
                        "Action": [
                            "ram:*"
                        ],
                        "Resource": [
                            "acs:ram:*:*:domain/*",
                            "acs:ram:*:*:application/*"
                        ],
                        "Effect": "Allow"
                    }
                ]
            }
            ```


**Note:** After the preceding role and policies are created, you can activate and use the fully managed Flink service.

## Manual authorization \(method 2\)

If the fully managed Flink service becomes unavailable because you accidentally delete the AliyunStreamAsiDefaultRole role or modify the authorization policy, you can perform the following steps to delete the stack, RAM role, and RAM policy of the Resource Orchestration Service \(ROS\) first. Then, log on to the Realtime Compute for Apache Flink console and perform authorization again.

1.  Delete the stack of ROS.

    1.  Log on to the [ROS console](http://ros.console.aliyun.com).

    2.  In the left-side navigation pane, click **Stacks**.

    3.  In the top navigation bar, change the region to **China \(Hangzhou\)**.

    4.  In **Stack Name**, enter FlinkServerlessStack and FlinkOnAckStack separately. Then, click the ![Search icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1811484161/p127081.png) icon.

        **Note:**

        -   FlinkServerlessStack: the unified name of the ROS stack of fully managed Flink
        -   FlinkOnAckStack: the unified name of the ROS stack of Container Service for Kubernetes \(ACK\)
    5.  Find the stack that you want to delete, and click **Delete** in the **Actions** column. In the dialog box that appears, click **OK**.

2.  Delete the RAM role.

    1.  Log on to the RAM console.

        -   Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
        -   Log on to the [RAM console](https://signin.alibabacloud.com/login.htm) as a RAM user.
    2.  In the left-side navigation pane, click **RAM Roles**.

    3.  In the search box, enter AliyunStreamAsiDefaultRole.

    4.  In the **Actions** column, click **Delete**. In the dialog box that appears, click **OK**.

        **Note:** The role can be deleted only after you click the AliyunStreamAsiDefaultRole name to go to the role details page and **remove permissions**.

3.  Delete the RAM policy.

    1.  Log on to the RAM console.

        -   Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
        -   Log on to the [RAM console](https://signin.alibabacloud.com/login.htm) as a RAM user.
    2.  In the left-side navigation pane, choose **Permissions** \> **Policies**.

    3.  In the search box, enter AliyunStreamAsiDefaultRolePolicy0, AliyunStreamAsiDefaultRolePolicy1, and FlinkServerlessPolicy separately.

    4.  In the **Actions** column, click **Delete**. In the dialog box that appears, click **OK**.

4.  In the Realtime Compute for Apache Flink console, perform authorization again.

    1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

    2.  Click **Purchase** under **Fully Managed Flink**.

    3.  On the **Authorization Request** page, click **Authorize in RAM**.

        ![Authorize](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8098574161/p110414.png)

    4.  On the **Cloud Resource Access Authorization** page, click **Confirm Authorization Policy** at the bottom of the page.

        ![AliyunStreamAsiDefaultRole](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8098574161/p110415.png)


## Permissions

-   Permissions on ECS resources

    To access the development console from the Internet, you must activate Elastic IP Address \(EIP\) by using your account. In addition, to connect resources in a VPC, you must create elastic network interfaces \(ENIs\) in the VPC. These ENIs are added to the dedicated security group of fully managed Flink. Therefore, the fully managed Flink service must have the operation permissions on the EIP, the security group, and the ENIs.

    **Note:** Data transfer costs are incurred if the EIP is used for access over the Internet.

    |Permission \(Action\)|Description|
    |---------------------|-----------|
    |ecs:AssociateEipAddress|Applies for an EIP so that you can access the fully managed Flink service over the Internet.|
    |ecs:AttachNetworkInterface|Allows the fully managed Flink service to bind your ENI to the resource pool of fully managed Flink.|
    |ecs:AuthorizeSecurityGroup|The fully managed Flink service creates a security group. This permission is required to add an inbound security group rule to the security group.|
    |ecs:AuthorizeSecurityGroupEgress|The fully managed Flink service creates a security group. This permission is required to add an outbound security group rule to the security group.|
    |ecs:CreateNetworkInterface|Allows the fully managed Flink service to create an ENI in your VPC and allows the fully managed Flink service to connect to your VPC.|
    |ecs:CreateNetworkInterfacePermission|Allows the fully managed Flink service to query ENIs.|
    |ecs:CreateSecurityGroup|The fully managed Flink service creates a security group. This permission is required to create the security group.|
    |ecs:DeleteNetworkInterface|Deletes the ENIs of the related resources after the Flink task is completed.|
    |ecs:DeleteNetworkInterfacePermission|Allows the fully managed Flink service to have the permissions to unbind your ENI.|
    |ecs:DeleteSecurityGroup|The fully managed Flink service creates a security group. This permission is required to delete the security group.|
    |ecs:DescribeNetworkInterfacePermissions|Allows you to unbind your ENI from the Flink Serverless resource pool.|
    |ecs:DescribeNetworkInterfaces|Allows the fully managed Flink service to query ENIs.|
    |ecs:DescribeSecurityGroupAttribute|Allows the fully managed Flink service to query the security group rules of a security group.|
    |ecs:DescribeSecurityGroupReferences|Allows the fully managed Flink service to query security groups and security group-level authorization.|
    |ecs:DescribeSecurityGroups|Allows the fully managed Flink service to query basic information about the created security groups.|
    |ecs:DetachNetworkInterface|Allows the fully managed Flink service to unbind your ENI from the resource pool of fully managed Flink.|
    |ecs:JoinSecurityGroup|Allows the fully managed Flink service to add ENIs to the specified security group.|
    |ecs:LeaveSecurityGroup|Allows the fully managed Flink service to remove ENIs from the specified security group.|
    |ecs:ModifyNetworkInterfaceAttribute|Allows the fully managed Flink service to modify information about an ENI, such as the name, the description, and the security group to which the ENI belongs.|
    |ecs:ModifySecurityGroupAttribute|Allows the fully managed Flink service to change the name or description of a security group.|
    |ecs:ModifySecurityGroupPolicy|Allows the fully managed Flink service to modify the access control policy within the security group.|
    |ecs:ModifySecurityGroupRule|Allows the fully managed Flink service to modify the descriptions of security group inbound rules.|
    |ecs:RevokeSecurityGroup|Allows the fully managed Flink service to delete a security group inbound rule.|
    |ecs:RevokeSecurityGroupEgress|Allows the fully managed Flink service to delete a security group outbound rule.|
    |ecs:UnassociateEipAddress|Allows the fully managed Flink service to release EIPs.|

-   Permissions on SLB resources

    To access the development console from the Internet, you must activate pay-as-you-go SLB by using your account. Therefore, you need the relevant permissions to manage SLB.

    **Note:** Pay-as-you-go SLB incurs additional fees.

    |Permission \(Action\)|Description|
    |---------------------|-----------|
    |slb:AddBackendServers|Allows the fully managed Flink service to change the backend of SLB.|
    |slb:AddListenerWhiteListItem|Allows the fully managed Flink service to add an IP address to the access whitelist of a listener.|
    |slb:AddTags|Allows the fully managed Flink service to add tags to the specified SLB instance.|
    |slb:AddVServerGroupBackendServers|Allows the fully managed Flink service to add backend servers to the specified backend server group.|
    |slb:CreateLoadBalancer|Allows the fully managed Flink service to create an SLB instance.|
    |slb:CreateLoadBalancerHTTPListener|Allows the fully managed Flink service to create an HTTP listener.|
    |slb:CreateLoadBalancerHTTPSListener|Allows the fully managed Flink service to create an HTTPS listener.|
    |slb:CreateLoadBalancerTCPListener|Allows the fully managed Flink service to create a TCP listener.|
    |slb:CreateLoadBalancerUDPListener|Allows the fully managed Flink service to create a UDP listener.|
    |slb:CreateRules|Allows the fully managed Flink service to create routing methods for the specified HTTP or HTTPS listener.|
    |slb:CreateVServerGroup|Allows the fully managed Flink service to add backend server groups and add backend servers to the specified backend server group.|
    |slb:DeleteLoadBalancer|Allows the fully managed Flink service to delete an SLB instance.|
    |slb:DeleteLoadBalancerListener|Allows the fully managed Flink service to delete a backend listener.|
    |slb:DeleteRules|Allows the fully managed Flink service to delete the routing methods of the specified HTTP or HTTPS listener.|
    |slb:DeleteVServerGroup|Allows the fully managed Flink service to delete a server group.|
    |slb:DescribeHealthStatus|Allows the fully managed Flink service to query the health status of backend servers.|
    |slb:DescribeListenerAccessControlAttribute|Allows the fully managed Flink service to query the whitelist configurations of the specified SLB listener.|
    |slb:DescribeLoadBalancerAttribute|Allows the fully managed Flink service to query the details of the specified SLB instance.|
    |slb:DescribeLoadBalancerHTTPListenerAttribute|Allows the fully managed Flink service to query the configurations of an HTTP listener.|
    |slb:DescribeLoadBalancerHTTPSListenerAttribute|Allows the fully managed Flink service to query the configurations of an HTTPS listener.|
    |slb:DescribeLoadBalancerTCPListenerAttribute|Allows the fully managed Flink service to query the configurations of a TCP listener.|
    |slb:DescribeLoadBalancerUDPListenerAttribute|Allows the fully managed Flink service to query the configurations of a UDP listener.|
    |slb:DescribeLoadBalancers|Allows the fully managed Flink service to query the created SLB instances.|
    |slb:DescribeRegions|Allows the fully managed Flink service to query the regions of available SLB instances.|
    |slb:DescribeRules|Allows the fully managed Flink service to query the routing methods that are configured for the specified listener.|
    |slb:DescribeTags|Allows the fully managed Flink service to query a list of tags.|
    |slb:DescribeVServerGroupAttribute|Allows the fully managed Flink service to query the details of a server group.|
    |slb:DescribeVServerGroups|Allows the fully managed Flink service to query a list of server groups.|
    |slb:ModifyLoadBalancerInstanceSpec|Allows the fully managed Flink service to modify the sample specifications of SLB.|
    |slb:ModifyLoadBalancerInternetSpec|Allows the fully managed Flink service to change the billing method \(by traffic or by bandwidth\) of an Internet-facing SLB instance.|
    |slb:ModifyLoadBalancerPayType|Allows the fully managed Flink service to change the purchase mode \(pay-as-you-go or subscription\) of an Internet-facing SLB instance.|
    |slb:RemoveBackendServers|Allows the fully managed Flink service to remove backend servers.|
    |slb:RemoveListenerWhiteListItem|Allows the fully managed Flink to delete an IP address from the whitelist of the listener.|
    |slb:RemoveVServerGroupBackendServers|Allows the fully managed Flink service to remove backend servers from the specified backend server group.|
    |slb:SetBackendServers|Allows the fully managed Flink service to set the backend server weight.|
    |slb:SetListenerAccessControlStatus|Allows the fully managed Flink service to specify whether to enable the whitelist of the specified listener for access control.|
    |slb:SetLoadBalancerHTTPListenerAttribute|Allows the fully managed Flink service to modify the configurations of an HTTP listener.|
    |slb:SetLoadBalancerHTTPSListenerAttribute|Allows the fully managed Flink service to modify the configurations of an HTTPS listener.|
    |slb:SetLoadBalancerName|Allows the fully managed Flink service to specify the name of an SLB instance.|
    |slb:SetLoadBalancerStatus|Allows the fully managed Flink service to specify the status of an SLB instance.|
    |slb:SetLoadBalancerTCPListenerAttribute|Allows the fully managed Flink service to modify the configurations of a TCP listener.|
    |slb:SetLoadBalancerUDPListenerAttribute|Allows the fully managed Flink service to modify the configurations of a UDP listener.|
    |slb:SetRule|Allows the fully managed Flink service to modify the routing methods of a vServer.|
    |slb:SetServerCertificateName|Allows the fully managed Flink service to set the name of a server certificate.|
    |slb:SetVServerGroupAttribute|Allows the fully managed Flink service to modify the configurations of a vServer group.|
    |slb:StartLoadBalancerListener|Allows the fully managed Flink service to start the specified listener.|
    |slb:StopLoadBalancerListener|Allows the fully managed Flink service to stop the specified listener.|

-   Permissions on OSS resources

    Your buckets in the selected region are listed.

    |Permission \(Action\)|Description|
    |---------------------|-----------|
    |oss:ListBuckets|Allows the fully managed Flink service to view a list of OSS buckets.|

-   Permissions on ARMS resources

    The metrics of fully managed Flink are stored in ARMS, so ARMS is activated for you.

    **Note:** You can store fully managed Flink-related metrics in ARMS for free.

    |Permission \(Action\)|Description|
    |---------------------|-----------|
    |arms:ListDashboards|Allows the fully managed Flink service to view the ARMS dashboard.|

-   Permissions on VPC resources

    When you activate fully managed Flink, the Describe permission on resources in the VPC is required.

    |Permission \(Action\)|Description|
    |---------------------|-----------|
    |vpc:DescribeVpcAttribute|Allows the fully managed Flink service to query the configuration information about the specified VPC.|
    |vpc:DescribeVpcs|Allows the fully managed Flink service to query the created VPCs.|
    |vpc:DescribeVSwitchAttributes|Allows the fully managed Flink service to query information about the specified vSwitch.|
    |vpc:DescribeVSwitches|Allows the fully managed Flink service to query the created vSwitches.|
    |vpc:DescribeRouteTableList|Allows the fully managed Flink service to query a list of route tables.|
    |vpc:DescribeRouteTables|Allows the fully managed Flink service to query the specified route table.|
    |vpc:DescribeRouteEntryList|Allows the fully managed Flink service to query a list of route entries in a route table.|
    |vpc:DescribeRouterInterfaceAttribute|Allows the fully managed Flink service to query the configurations of the router interface.|
    |vpc:DescribeRouterInterfaces|Allows the fully managed Flink service to query router interfaces.|
    |vpc:DescribeVRouters|Allows the fully managed Flink service to query a list of vRouters in the specified region.|

-   Permissions on RAM resources

    When you activate fully managed Flink, RAM-related permissions are required for configuring resources.

    |Permission \(Action\)|Description|
    |---------------------|-----------|
    |ram:\*|Allows you to add, delete, modify, and query the two RAM resources: domain and application.|


