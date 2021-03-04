# Manage projects

A project is a basic unit for managing jobs in fully managed Flink. All your configurations, jobs, and permissions are managed in a single project. You can create multiple projects and assign separate resources and permissions to each project. Resources and permissions \(configure members for projects\) are completely isolated among multiple projects.

After you purchase a fully managed Flink instance, the system generates a project named Workspace name-default by default. All initial resources belong to this project. You can create projects, delete projects, and reconfigure project resources in a workspace.

**Note:** Only Alibaba Cloud accounts have the permission to manage projects.

## Create a project

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **More** in the **Actions** column of the desired workspace.

3.  Click **Add Project**.

4.  Enter project information.

    ![Project information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9721484161/p176849.png)

    **Note:**

    -   Project names must be unique.
    -   One project requires at least 1 compute unit \(CU\). Therefore, the remaining allocable resources in the workspace must be greater than 1 CU. If the remaining resources in the workspace are insufficient, you must scale out the entire workspace first. After the scale-out is successful, you can add resources available for the project. For more information about how to scale out a workspace, see [Reconfigure workspace resources]().
5.  Click **OK**.

    After the project is created, you can click the project name to go to the development console and develop jobs in the project.


## Delete a project

Before you delete a project, make sure that no running jobs exist in the project. If a running job exists in the project, an error is reported. The system prompts you to stop the running job before you delete the project. After the project is deleted, metadata \(jobs, metrics, and resources\) in the project cannot be restored. The resources of the project can be used when other projects are scaled out.

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click ![Unfold button](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0821484161/p176852.png) on the left side of the desired workspace ID.

3.  In the **Actions** column of the desired project, click **Delete**.

4.  Click **OK**.

    **Note:** After you click **OK**, the jobs and data in your project cannot be restored. Proceed with caution.


## Reconfigure resources for a single project

If resources are allocated to a project, you can add or reduce available resources in the project, and scale out or scale in the project. Before a scale-out, make sure that the entire workspace has allocable resources. If the resources are insufficient, you must scale out the entire workspace first. After the scale-out is successful, you can add resources available for the project. For more information about how to scale out the entire workspace, see [Reconfigure workspace resources]().

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click ![Unfold button](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0821484161/p176852.png) on the left side of the desired workspace ID.

3.  In the **Actions** column of the desired project, click **Reconfigure Resources**.

4.  Adjust the slider to reconfigure resources.

5.  Click **OK**.


## Reconfigure resources for multiple projects

You can perform the following steps to adjust resources for multiple projects at a time:

1.  Log on to the [Realtime Compute for Apache Flink console](https://realtime-compute.console.aliyun.com/regions/cn-shanghai).

2.  On the **Fully Managed Flink** tab, click **Reconfigure Project Resources** in the **Actions** column of the desired workspace.

3.  On the right side of the desired project, adjust the slider to reconfigure resources.

    ![Reconfigure project resources](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0821484161/p176864.png)

4.  Click **OK**.


