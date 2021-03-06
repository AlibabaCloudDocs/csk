---
keyword: [node pool, create a node pool, Scale out a node pool]
---

# Manage a node pool

You can use a node pool to manage a set of nodes for a Container Service for Kubernetes \(ACK\) cluster. For example, you can manage the labels and taints that are added to the nodes in a node pool. This topic describes how to create a node pool in the ACK console.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The Kubernetes version of the cluster must be later than V1.9.

**Note:**

-   By default, you can deploy up to 100 nodes in an ACK cluster. To add more nodes, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
-   When you add an Elastic Compute Service \(ECS\) instance to a node pool, make sure that the ECS instance is associated with an elastic IP address \(EIP\) or a NAT gateway is configured for the virtual private cloud \(VPC\) where the ECS instance is deployed. In addition, make sure that the ECS instance can access the Internet. Otherwise, you cannot add the ECS instance.

ACK provides two types of node pools: regular node pools and managed node pools. Regular node pools include default node pools and custom node pools. You can enable auto scaling of custom node pools and managed node pools. For more information, see [Overview of node pools](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Overview of node pools.md).

## Create a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  In the upper-right corner of the Node Pools page, click **Create Node Pool**.

    In the upper-right corner of the Node Pools page, you can also click **Create Managed Node Pool** to create a managed node pool, or click **Configure Auto Scaling** to create an auto-scaling node pool.

6.  In the Create Node Pool dialog box, configure the node pool.

    For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md). The following table describes some of the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Quantity**|Specify the initial number of nodes in the node pool. If you do not want to create nodes in the node pool, set this parameter to 0.|
    |**Operating System**|Select the node operating system. The CentOS, Alibaba Cloud Linux, and Windows operating systems are supported.|
    |**Public IP**|If you select **Assign a Public IPv4 Address to Each Node**, public IPv4 addresses are assigned to the nodes in the node pool. You can connect to a node by using the assigned IP address. For more information about public IP addresses, see [Public IP addresses](/intl.en-US/Network/Instance IP addresses/IP addresses of ECS instances within VPCs.md). **Note:** If you select **Assign a Public IPv4 Address to Each Node**, do not select **Bind EIP** when you configure auto scaling. Otherwise, nodes cannot be added to the node pool. |
    |**ECS Label**|You can add labels to the selected Elastic Compute Service \(ECS\) instances.|
    |**Node Label**|You can add labels to the nodes in the node pool.|
    |**Custom Resource Group**|You can specify a resource group for scale-out. When the node pool is scaled out, nodes from the specified resource group are added to the node pool.|

7.  Click **Confirm Order**.

    On the Node Pools page, if the **state** of the node pool is **Initializing**, it indicates that the node pool is being created. After the node pool is created, the **state** of the node pool changes to **Active**.

    ![nodepool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5365359951/p95881.png)


## Scale out a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the cluster name. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  Find the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

6.  In the dialog box that appears, set the scale-out parameters.

    ![Scale out a node pool](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2835359951/p96553.png)

    For more information about how to set the parameters, see [Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Expand an ACK cluster.md). The following list describes some of the parameters:

    -   **Nodes to Add**: the number of nodes that you want to add to the node pool. You can create up to 100 clusters that belong to the current account. You can add up to 500 nodes at a time.
    -   **ECS Label**: You can add labels to the Elastic Compute Service \(ECS\) instances.
    -   **Node Label**: You can add labels to the nodes in the cluster.
    -   **Taints**: You can add taints to the nodes in the cluster.

        **Note:** If you select **Synchronize Node Labels and Taints**, the specified labels and taints are synchronized to the existing and newly added nodes.

7.  Click **Submit**.

    On the Node Pools page, the **state** of the node pool is **scaling** This indicates that the scale-out event is in progress. After the scale-out event is complete, the **state** of the node pool changes to **Active**.


## What to do next

After the node pool is created, you can go to the Node Pools page and perform the following operations on the node pool:

-   Click **Details** in the **Actions** column to view the details of the node pool.
-   Click **Edit** in the **Actions** column to modify the configurations of the node pool. For more information, see [Create a node pool](#section_eq0_lmv_4a7).

    **Note:** When you modify the configurations of the node pool, you can update the image version of the node operating system.

-   Click **Scale Out** in the **Actions** column to add nodes to the node pool.
-   Click the name of the node pool that you want to manage. You can perform the following operations on the details page of the node pool.
    -   Click the **Overview** tab to view information about the cluster, node pool, node configurations, and auto scaling settings.
    -   Click the **Nodes** tab to view information about the nodes in the node pool. You can select multiple nodes and remove them at a time.
    -   Click the **Nodes** tab. In the upper-right corner of the tab, you can click **Export** to export node information as comma-separated values \(CSV\) files.
    -   Click the **Nodes** tab. You can select **Display Only Failed Nodes** to view failed nodes.

**Related topics**  


[Overview of node pools](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Overview of node pools.md)

