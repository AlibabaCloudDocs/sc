---
keyword: [Fully managed Flink, RAM user authorization, authorization]
---

# Grant permissions to accounts

This topic describes how to grant permissions to Alibaba Cloud accounts or Resource Access Management \(RAM\) users in the development console of fully managed Flink.

If your RAM user is not authorized by your Alibaba Cloud account, you can view the **403** error after you log on to the development console of fully managed Flink.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, click **Development Console** in the **Actions** column of the desired workspace.

3.  In the left-side navigation pane, choose **Administration** \> **Members**.

4.  Click **Add Member**.

5.  Specify **Role** and enter **Member** information.

    ![Add a member](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2845434161/p133336.png)

    -   Role

        The following table describes the functional differences between different roles.

        |Function|owner|editor|viewer|
        |--------|-----|------|------|
        |View a job list|Y|Y|Y|
        |Start and terminate a job|Y|Y|N|
        |Modify job configurations|Y|Y|N|
        |Upload a JAR file|Y|Y|N|
        |Compile SQL statements|Y|Y|N|
        |Register a user-defined function \(UDF\)|Y|Y|N|
        |Register metadata|Y|Y|N|
        |View a job template|Y|Y|Y|
        |Add, delete, or modify a job template|Y|Y|N|
        |Manage members|Y|N|N|

    -   **Member**: the user identifier \(UID\) or display name of a RAM user. You can obtain the UID and display name of a RAM user by performing the following steps:
        1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using an Alibaba Cloud account.
        2.  In the left-side navigation pane, choose **Identities** \> **Users**.
        3.  Click the logon name of the desired RAM user.
        4.  In the **Basic Information** section, view the UID and the display name.
6.  Click **OK**.


