# Change resource configurations

This topic describes how to scale out or scale in a Realtime Compute for Apache Flink cluster or project.

## Change resource configurations of a cluster

**Note:**

-   Fees after the resource configuration change:
    -   Scale-out \(**Master Scale-Up** or **Slave Scale-Out**\): You must pay the relevant upgrade fees.
    -   Scale-in \(**Master Scale-Down** or **Slave Scale-In**\): The refund from the scale-in is returned to your account.
-   After you perform **Master Scale-Up** or **Slave Scale-Out**, you must add required IP addresses to a whitelist of the database. For more information, see [Configure a whitelist for accessing storage resources](/intl.en-US/Exclusive Mode/Flink SQL Development Guide/Data storage/Configure a whitelist for accessing storage resources.md).

You can modify the configuration of **Master Specifications** or **Slave Quantity** to change the configuration of the cluster.

1.  Go to the **Project Management** page.

    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

    2.  Move the pointer over the username in the upper-right corner.

    3.  Click **Project Management**.

2.  In the left-side navigation pane, choose **Cluster Management** \> **Clusters**.

3.  On the Clusters page, find your cluster, click **More** in the **Actions** column, and select **Scale Out** or **Scale In**.

4.  Change resource configurations.

    The following example describes two scale-out methods:

    -   **Master Scale-Up \(use higher specifications\)**

        1.  On the Update page, select **Master Scale-Up** for Upgrade Method.
        2.  Specify **Master Specifications**.
        **Note:**

        -   You can change the master specifications but cannot change the number of master nodes.
        -   If you use a high availability cluster configured with three master nodes, the system upgrades all three master nodes at the same time when you select **Master Scale-Up**.
    -   **Slave Scale-Out \(increase the number of slave nodes\)**

        1.  On the Update page, select **Slave Scale-Out** for Upgrade Method.
        2.  Increase the value of **Slave Quantity**.
        **Note:**

        -   If the number of CUs is insufficient, we recommend that you select **Slave Scale-Out** and increase the value of **Slave Quantity**.
        -   If you are unable to complete payment after you specify Slave Quantity and read and select Realtime Compute Exclusive Mode \(Subscription\) Terms of Service, add a VSwitch and enter the ID of the new VSwitch in **vSwitchId**. After verification, you can complete the payment for the scale-out. For more information about how to add a VSwitch, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md).
        -   The slave nodes that you want to add must be located in the same zone as the cluster.
        -   You cannot change the slave specifications for an existing Realtime Compute for Apache Flink cluster in exclusive mode. You can only increase the value of **Slave Quantity**. If you want to change the slave specifications, you must purchase a new cluster.
5.  Read and select Realtime Compute Exclusive Mode \(Subscription\) Terms of Service.

6.  Click **Pay**.


## Change resource configurations of a project

**Note:**

-   If the computing capability of existing resources does not meet your business requirements, you can scale out the project to improve the computing capability of the system.
-   If the computing capability of existing computing resources is excessive to meet your business requirements, you can downgrade the resource configuration to reduce costs.

1.  Go to the **Project Management** page.

    1.  Log on to the [Realtime Compute development platform](https://stream-ap-southeast-3.console.aliyun.com).

    2.  Move the pointer over the username in the upper-right corner.

    3.  Click **Project Management**.

2.  In the left-side navigation pane, choose **Project Management** \> **Projects**.

3.  Change resource configurations:

    1.  On the Projects page, find your project and click **Scale In/Out** in the **Actions** column.

    2.  In the Scale In/Out dialog box, specify Specified CUs.

    3.  Click **OK**.


