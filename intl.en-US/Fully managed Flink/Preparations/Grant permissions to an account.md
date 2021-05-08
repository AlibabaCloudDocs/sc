---
keyword: [fully managed Flink, RAM user authorization, authorization]
---

# Grant permissions to an account

This topic describes how to grant permissions to an Alibaba Cloud account or a Resource Access Management \(RAM\) user in the development console of fully managed Flink.

If your RAM user is not authorized by your Alibaba Cloud account, you can view error 403 after you log on to the development console of fully managed Flink.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/console/cell?spm=a2c4g.11186623.2.16.1a8023a9J8TiPV).

2.  On the **Fully Managed Flink** tab, find the desired workspace, and click **Development Console** in the **Actions** column.

3.  In the left-side navigation pane, choose **Administration** \> **Members**.

4.  Click **Add Member**.

5.  Specify **Role** and enter **Member** information.

    ![Add a member](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2845434161/p133336.png)

    -   Roles

        The following table describes the differences between operations that are supported by different roles.

        |Operation|owner|editor|viewer|
        |---------|-----|------|------|
        |View jobs|Y|Y|Y|
        |Start and terminate a job|Y|Y|N|
        |Modify job configurations|Y|Y|N|
        |Upload a JAR file|Y|Y|N|
        |Compile SQL statements|Y|Y|N|
        |Register a user-defined function \(UDF\)|Y|Y|N|
        |Register metadata|Y|Y|N|
        |View a job template|Y|Y|Y|
        |Add, delete, or modify a job template|Y|Y|N|
        |Manage members|Y|N|N|

    -   **Member**: the user identifier \(UID\) or display name of a RAM user. To obtain the UID and display name of a RAM user, you can perform the following steps:
        1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.
        2.  In the left-side navigation pane, choose **Identities** \> **Users**.
        3.  On the Users page, click the logon name of the desired RAM user in the User Logon Name/Display Name column.
        4.  In the **Basic Information** section, view the UID and display name.
6.  Click **OK**.

    **Note:** Another Alibaba Cloud account or RAM user who has logged on to the development console of fully managed Flink must log on to the console again after they are authorized. Otherwise, the account or RAM user cannot view project information.


