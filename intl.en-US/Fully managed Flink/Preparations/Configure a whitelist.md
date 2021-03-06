# Configure a whitelist

In normal cases, the upstream and downstream storage devices of fully managed Flink deny access from external devices by default. Therefore, you must add the Classless Inter-Domain Routing \(CIDR\) block of the vSwitch of fully managed Flink to the whitelist of your desired storage device.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the Fully Managed Flink tab, find the desired workspace, click **More** in the **Actions** column, and then select **Workspace Details**.

3.  In the **Workspace Details** dialog box, view **CIDR Block** about the vSwitch of **fully managed Flink**.

4.  Add the CIDR block of the vSwitch of **fully managed Flink** to the whitelist of your desired storage device.

    For example, you must configure a whitelist for an ApsaraDB RDS for MySQL database. For more information, see [Configure an IP address whitelist for an ApsaraDB RDS for MySQL instance](/intl.en-US/RDS MySQL Database/Data security/Set the whitelist/Configure an IP address whitelist for an ApsaraDB RDS for MySQL instance.md).

    **Note:**

    -   If you add a vSwitch later, you must also add the CIDR block of the new vSwitch to the whitelist of the desired storage device.
    -   If your vSwitch is not in the same zone as the upstream and downstream storage, the network can be connected after you add the CIDR block of the vSwitch to the whitelist.

