# Alibaba Cloud account and RAM user authorization

This topic describes the account model, Alibaba Cloud account and Resource Access Management \(RAM\) user authorization, and account verification in Realtime Compute for Apache Flink.

## Account model

Log on to the [RAM console](https://ram.console.aliyun.com/) to configure your account.

Realtime Compute for Apache Flink allows you to log on to its console by using an Alibaba Cloud account or as a RAM user. You can use an Alibaba Cloud account to purchase Realtime Compute for Apache Flink and create projects. You can also authorize a RAM user to use the Realtime Compute for Apache Flink projects that are created by using an Alibaba Cloud account.

In Realtime Compute for Apache Flink, a project belongs to an Alibaba Cloud account. If a project requires collaboration of multiple persons, you must use the Alibaba Cloud account + RAM user model.

## Alibaba Cloud account + RAM user model

Realtime Compute for Apache Flink uses the Alibaba Cloud account + RAM user model to manage accounts and permissions. To log on to the Realtime Compute for Apache Flink console as a RAM user, perform the following steps:

1.  Initialize RAM.

    You must initialize RAM when you use RAM for the first time. For more information, see [Configure the password policy for RAM users](/intl.en-US/Security Settings/Passwords/Configure the password policy for RAM users.md) and [Set security policies for RAM users](/intl.en-US/Security Settings/Basic security settings/Set security policies for RAM users.md).

2.  Create a RAM user.

    You can allocate RAM users to the developers of Realtime Compute for Apache Flink. For more information about how to create a RAM user, see [Create a RAM user](/intl.en-US/RAM User Management/Basic operations/Create a RAM user.md).

3.  Create a custom permission policy.

    For more information about how to create a custom permission policy in the RAM console, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

    -   The following code demonstrates a permission policy of Realtime Compute for Apache Flink:

        ```
        {    
           "Version": "1",
           "Statement": [
            { 
             "Action": "stream:*", 
              "Resource": "acs:stream:*:*:*", 
              "Effect": "Allow"
            }, 
            { 
              "Action": "ram:PassRole",
              "Resource": "acs:ram:*:*:*",
              "Effect": "Allow" 
           } 
          ] 
        }
        ```

    -   Realtime Compute for Apache Flink supports `project-level` permission policies. This way, you can authorize RAM users to access different `projects`. To authorize a RAM user to access a single `project`, modify the preceding code in the following way. Replace `projectname` with the name of a project you want to authorize.
        -   Before the modification

            ```
            "Resource": "acs:stream:*:*:*"
            ```

        -   After the modification

            ```
            "Resource":"acs:stream:*:*:projectname"
            ```

4.  Grant permissions to RAM users or RAM user groups.

    Attach the preceding policy to specified RAM users or RAM user groups. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md) and [Grant permissions to a RAM user group](/intl.en-US/RAM User Group Management/Grant permissions to a RAM user group.md).

5.  Log on to the Realtime Compute for Apache Flink console as a RAM user.

    RAM users can log on to the Realtime Compute for Apache Flink console by using the **RAM user logon URL**.

    **Note:** You can find the RAM user logon URL under **RAM user logon** in the **Account Management** section of the **Overview** page in the [RAM console](https://ram.console.aliyun.com/).


## Account verification

To ensure account security, Realtime Compute for Apache Flink provides the account verification feature. If you have not operated a job for a long period of time, the system sends a text message and an email to you for account verification.

