---
keyword: [cluster networks, VPC, Terway and Flannel]
---

# Plan CIDR blocks for an ACK cluster

When you create a Container Service for Kubernetes \(ACK\) cluster, you must specify a VPC, vSwitches, the CIDR block of pods, and the CIDR block of Services. Therefore, we recommend that you plan the IP address of each Elastic Compute Service \(ECS\) instance in the cluster, the CIDR block of pods, and the CIDR block of Services before you create an ACK cluster. This topic describes how to plan CIDR blocks for an ACK cluster deployed in a virtual private cloud \(VPC\) and how each CIDR block is used.

## VPC-related CIDR blocks and cluster-related CIDR blocks

Before you create a VPC, you must plan the CIDR block of the VPC and the CIDR blocks of vSwitches in the VPC. Before you create an ACK cluster, you must plan the CIDR block of pods and the CIDR block of Services. ACK supports the Terway and Flannel plug-ins. The following shows the network architecture of an ACK cluster that uses Terway or Flannel.

![terway](../images/p211963.png "Terway")

![Flannel](../images/p211964.png "Flannel")

To install Terway or Flannel in an ACK cluster, you must specify CIDR blocks as described in the following table.

|Parameter|Terway|Flannel|
|---------|------|-------|
|**VPC**|When you create a VPC, you must select a CIDR block for the VPC. Valid values: 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.|
|**VSwitch**|The IP addresses of ECS instances are assigned from the vSwitch. This way, the nodes in a cluster can communicate with each other. The CIDR blocks that you specify when you create vSwitches in the VPC must be subsets of the VPC CIDR block. This means that the CIDR blocks of vSwitches must be smaller than or the same as the VPC CIDR block. When you set this parameter, take note of the following items:-   Select one or more vSwitches in the VPC.
-   IP addresses from the CIDR block of a vSwitch are allocated to the ECS instances that are attached to the vSwitch.
-   You can create multiple vSwitches in a VPC. However, the CIDR blocks of these vSwitches must not overlap with each other.
-   The vSwitches and pod vSwitches must be in the same zone. For more information about zones, see [Regions and zones]().

|The IP addresses of ECS instances are assigned from the vSwitch. This way, the nodes in a cluster can communicate with each other. The CIDR blocks that you specify when you create vSwitches in the VPC must be subsets of the VPC CIDR block. This means that the CIDR blocks of vSwitches must be smaller than or the same as the VPC CIDR block. When you set this parameter, take note of the following items:-   Select one or more vSwitches in the VPC.
-   IP addresses from the CIDR block of a vSwitch are allocated to the ECS instances that are attached to the vSwitch.
-   You can create multiple vSwitches in a VPC. However, the CIDR blocks of these vSwitches must not overlap with each other. |
|**Pod VSwitch**|The IP addresses of pods are assigned from the CIDR block of the pod vSwitch. This way, the pods can communicate with each other. A pod is a group of containers in a Kubernetes cluster. Each pod has an IP address. The CIDR blocks that you specify when you create pod vSwitches in the VPC must be subsets of the VPC CIDR block. When you set this parameter, take note of the following items:-   Select one or more vSwitches in the VPC.
-   In an ACK cluster that has Terway installed, the IP addresses of pods are allocated from pod vSwitches.
-   The CIDR blocks of pod vSwitches must not overlap with those of **vSwitches**.
-   The CIDR blocks of pod vSwitches must not overlap with the CIDR block specified by **Service CIDR**.
-   The vSwitches and pod vSwitches must be in the same zone. For more information about zones, see [Regions and zones]().

For example, if the VPC CIDR block is 172.16.0.0/12, the CIDR block of a pod vSwitch cannot be 172.16.0.0/16 or 172.17.0.0/16, because these CIDR blocks are subsets of 172.16.0.0/12.

|You do not need to set this parameter if you want to install the Flannel plug-in.|
|**Pod CIDR Block**|You do not need to set this parameter if you want to install the Terway plug-in.|The IP addresses of pods are allocated from the pod CIDR block. This way, the pods can communicate with each other. A pod is a group of containers in a Kubernetes cluster. Each pod has an IP address. When you set this parameter, take note of the following items:-   Enter a CIDR block in the Pod CIDR Block field.
-   The CIDR block of pods must not overlap with the CIDR blocks of **vSwitches**.
-   The CIDR block of pods must not overlap with the CIDR block specified by **Service CIDR**.

For example, if the VPC CIDR block is 172.16.0.0/12, the pod CIDR block cannot be 172.16.0.0/16 or 172.17.0.0/16, because these CIDR blocks are subsets of 172.16.0.0/12. |
|**Service CIDR**|The CIDR block of Services. Service is a concept in Kubernetes. Each `ClusterIP` Service has an IP address. When you set this parameter, take note of the following items:-   The IP address of a Service applies only within the Kubernetes cluster.
-   The CIDR block of Services must not overlap with the CIDR blocks of **vSwitches**.
-   The CIDR block of Services must not overlap with the CIDR blocks of **pod vSwitches** and the **pod CIDR block**. |

## Network planning

To use Kubernetes clusters that are supported by ACK on Alibaba Cloud, you must first set up networks for the clusters based on the cluster size and business scenarios. You can refer to the following tables to set up networks for a cluster. Change specifications as needed in uncovered scenarios.



|Cluster size|Scenario|VPC|Zone|
|------------|--------|---|----|
|≤ 100 nodes|Ordinary business|Single VPC|1|
|Unlimited|Cross-zone deployment is required|Single VPC|≥ 2|
|Unlimited|High reliability and cross-region deployment are required|Multiple VPCs|≥ 2|

The following examples show how to plan CIDR blocks for clusters in different scenarios:

-   **A cluster that uses Flannel**

    |VPC CIDR block|vSwitch CIDR block|Pod CIDR block|Service CIDR block|
    |--------------|------------------|--------------|------------------|
    |192.168.0.0/16|192.168.0.0/24|172.20.0.0/16|172.21.0.0/20|

-   **Each pod is assigned an exclusive elastic network interface \(ENI\)** or **the IPVlan mode is enabled**

    |VPC CIDR block|vSwitch CIDR block|CIDR block of pod vSwitch|Service CIDR block|
    |--------------|------------------|-------------------------|------------------|
    |192.168.0.0/16|192.168.0.0/19|192.168.32.0/19|172.21.0.0/20|

-   **A cluster that is deployed across zones**

    |VPC CIDR block|vSwitch CIDR block|CIDR block of pod vSwitch|Service CIDR block|
    |--------------|------------------|-------------------------|------------------|
    |192.168.0.0/16|Zone I 192.168.0.0/19|192.168.32.0/19|172.21.0.0/20|
    |Zone J 192.168.64.0/19|192.168.96.0/19|


## How to plan CIDR blocks

-   **Scenario 1: one VPC and one Kubernetes cluster**

    This is the simplest scenario. The CIDR block of a VPC is specified when you create the VPC. When you create a cluster in the VPC, make sure that the CIDR block of pods and the CIDR block of Services do not overlap with the VPC CIDR block.

-   **Scenario 2: one VPC and multiple Kubernetes clusters**

    Assume that you want to create more than one cluster in a VPC.

    -   The CIDR block of a VPC is specified when you create the VPC. When you create clusters in the VPC, make sure that the VPC CIDR block, Service CIDR block, and pod CIDR block of each cluster do not overlap with each other.
    -   The Service CIDR blocks of the clusters can overlap with each other. However, the pod CIDR blocks must not overlap with each other.
    -   In the default network mode \(Flannel\), the packets of pods must be forwarded by the VPC router. ACK automatically generates a route table for each destination pod CIDR block on the VPC router.
    **Note:** In this case, a pod in one cluster can communicate with the pods and ECS instances in another cluster. However, the pod cannot communicate with the Services in another cluster.

-   **Scenario 3: two connected VPCs**

    If two VPCs are connected, you can use the route table of one VPC to specify which packets are sent to the other VPC. The CIDR block of VPC 1 is 192.168.0.0/16 and the CIDR block of VPC 2 is 172.16.0.0/12, as shown in the following figure. You can use the route table of VPC 1 to forward all packets that are destined for 172.16.0.0/12 to VPC 2.

    ![Route tables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4635359951/p8765.png)

    |VPC|CIDR block|Destination CIDR block|Destination VPC|
    |---|----------|----------------------|---------------|
    |VPC 1|192.168.0.0/16|172.16.0.0/12|VPC 2|
    |VPC 2|172.16.0.0/12|192.168.0.0/16|VPC 1|

    In this scenario, make sure that the following conditions are met when you create a cluster in VPC 1 or VPC 2:

    -   The CIDR blocks of the cluster do not overlap with the CIDR block of VPC 1.
    -   The CIDR blocks of the cluster do not overlap with the CIDR block of VPC 2.
    -   The CIDR blocks of the cluster do not overlap with those of other clusters.
    -   The CIDR blocks of the cluster do not overlap with those of pods.
    -   The CIDR blocks of the cluster do not overlap with those of Services.
    In this example, you can set the pod CIDR block of the cluster to a subset of 10.0.0.0/8.

    **Note:** All IP addresses in the destination CIDR block of VPC 1 can be considered as occupied. Therefore, the CIDR blocks of the cluster must not overlap with the destination CIDR block.

    To access pods in VPC 1 from VPC 2, you must configure a route in VPC 2. The route must point to the pod CIDR block of a cluster in VPC 1.

-   **Scenario 4: a VPC connected to a data center**

    If a VPC is connected to a data center, packets of specific CIDR blocks are routed to the data center. In this case, the pod CIDR block of a cluster in the VPC must not overlap with these CIDR blocks. To access pods in the VPC from the data center, you must configure a route in the data center to enable VBR-to-VPC peering connection based on virtual border routers \(VBRs\).


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Overview.md)

[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)

[Use network policies for access control](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use network policies for access control.md)

[FAQ](/intl.en-US/User Guide for Kubernetes Clusters/Network/FAQ.md)
