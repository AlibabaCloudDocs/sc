# Pricing

This topic describes the billing methods of fully managed Flink for commercial use.

## Billing methods

The basic billing unit of fully managed Flink is compute unit \(CU\), which indicates computing resources. 1 CU is equal to 1 CPU core and 4 GB of memory. The number of CUs determines the CPU computing capability of the underlying system in Realtime Compute for Apache Flink. Fully managed Flink supports the following billing methods:

-   Subscription

    A billing method that allows you to use fully managed Flink resources only after you pay for them. You are charged based on the resource size and subscription period.

    **Note:** Renew your instances at the earliest opportunity. If you do not renew an instance within seven days after it expires, the instance is released.

-   Pay-as-you-go

    A billing method that allows you to use fully managed Flink resources before you pay for them. When you purchase resources, you do not need to select resource types. You are charged based on the resources that you actually use each minute.

    Resource status changes after an overdue payment: If the available balance of your account is insufficient to settle a payment, the system determines that your account has an overdue payment. The balance includes cash, vouchers, coupons, and prepaid coupons. If you do not top up your account within the specified period of time, you will not be able to use the resources.

    **Note:** An overdue payment is generated if the available credit is less than zero. If the overdue payment exceeds the amount or time limit of the extended suspension benefit, the pay-as-you-go instances enter the service suspension process. However, Alibaba Cloud still retains your code logic and job status for seven days from the day when Alibaba Cloud suspends your service. You are charged for the storage fees during this period. If you do not pay the service charge owed within seven days, Alibaba Cloud stops providing you with the service, and all your data stored on Alibaba Cloud is deleted and cleared and cannot be restored.


## Billable items

The billable items of fully managed Flink consist of two parts:

-   Management resources: When you create an instance, the system deploys a development console for each cluster. Each development console and necessary components require about 2 CUs of management resources.
-   Computing resources: the actual resources that are used for computing. You are charged based on the subscription or pay-as-you-go billing method. The billing unit is CU.

**Note:**

-   Each session cluster that is running consumes additional 0.5 CUs.
-   The service immediately stops after it expires. After the service stops, the jobs that are running online are not affected. However, you cannot submit new jobs. If you do not renew the service within seven days after it expires, the resources are released and your data is deleted.
-   Server Load Balancer \(SLB\) allows you to connect to the console of fully managed Flink over the Internet. If you do not renew your SLB instance within seven days after an overdue payment is generated, the SLB instance is released. If this happens, you cannot connect to the console of fully managed Flink over the Internet. In this case, you must purchase a fully managed Flink instance again. For more information, see [SLB overdue payments](/intl.en-US/Classic Load Balancer/CLB Pricing/Overdue payments.md).

