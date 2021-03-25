# Service types

Before you purchase Realtime Compute for Apache Flink, you must understand the features of each service type so you can select suitable instances for your business.

## Features

Realtime Compute for Apache Flink has five service types. The following table describes the features of each service type.

|Service type|Product line|Release status|Deployment mode|Compute engine|Development platform|
|------------|------------|--------------|---------------|--------------|--------------------|
|**Fully-managed Flink**|New product line|Released|Kubernetes-based deployment|Flink|VVP|
|**Semi-managed Flink \(ACK-based\)**|New product line|Released|Kubernetes-based deployment|Flink|VVP|
|**Semi-managed Flink \(EMR-based\)****Note:** A Realtime Compute for Apache Flink cluster of this service type is a dataflow cluster running on Alibaba Cloud E-MapReduce.

|New product line|Released|YARN-based deployment|Flink|VVP|
|**Blink exclusive cluster \(original product line\)**|Original product line|Released|YARN-based deployment|Blink|Bayes|
|**Blink shared cluster \(original product line\)**|Original product line|Phased-out|YARN-based deployment|Blink|Bayes|

## Selection suggestions

-   **Fully-managed Flink**: If your company or team wants to focus on business development without spending extra time on cluster O&M, we recommend that you use this service type.
-   **Semi-managed Flink \(ACK-based\)**: If your company or team wants to gain full control over all the cluster resources on the basis of business development and is familiar with Container Service for Kubernetes \(ACK\), we recommend that you use this service type.
-   **Semi-managed Flink \(EMR-based\)**: If your company or team wants to gain full control over all the cluster resources on the basis of business development and is familiar with YARN or Alibaba Cloud E-MapReduce, we recommend that you use this service type.
-   **Blink exclusive cluster \(original product line\)**: If you have purchased a Blink exclusive cluster of Realtime Compute for Apache Flink, you can continue to use this service type.
-   **Blink shared cluster \(original product line\)**: This service type is phased out. You can only scale in or out the existing Blink shared clusters. We recommend that you do not use this service type.

