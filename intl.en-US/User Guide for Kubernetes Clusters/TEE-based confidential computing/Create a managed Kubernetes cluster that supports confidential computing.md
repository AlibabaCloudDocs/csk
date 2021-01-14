---
keyword: [managed Kubernetes cluster that supports confidential computing, ECS Bare Metal instance, Intel \(R\) SGX application]
---

# Create a managed Kubernetes cluster that supports confidential computing

This topic describes how to create a managed Kubernetes cluster that supports confidential computing in the Container Service for Kubernetes \(ACK\) console.

ACK and Resource Access Management \(RAM\) are activated.

You can log on to the [ACK](https://cs.console.aliyun.com/) to activate ACK and the [RAM console](https://ram.console.aliyun.com/) to activate RAM.

**Note:**

Note the following limits when you use ACK:

-   SLB instances that are created together with the ACK cluster support only the pay-as-you-go billing method.
-   ACK clusters support only Virtual Private Cloud \(VPC\) networks.
-   Each account can consume only a limited amount of computing resources. You fail to create clusters if you do not have sufficient computing resources. When you create clusters, make sure that you have sufficient resources. To increase the quota of computing resources for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).
    -   You can create up to 50 clusters across regions for each account. You can deploy up to 100 nodes in each cluster. To increase the quota of clusters or nodes for your account, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

        **Note:** By default, you can add up to 48 route entries to each VPC network in an ACK cluster. This means that you can deploy up to 48 nodes in an ACK cluster that uses Flannel. An ACK cluster that uses Terway is not subject to this limit. To increase the quota of route entries for a VPC network, [submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

    -   You can create up to 100 security groups under each account.
    -   You can create up to 60 pay-as-you-go SLB instances under each account.
    -   You can create up to 20 elastic IP addresses \(EIPs\) under each account.
-   To create a confidential computing cluster, you must set the parameters based on the information in the following table. Otherwise, you cannot run Intel \(R\) Software Guard Extensions \(SGX\) applications in the cluster.

    |Parameter|Description|
    |---------|-----------|
    |**Zone**|Only ECS Bare Metal instances of the ecs.ebmhfg5.2xlarge type support confidential computing clusters. Make sure that this instance type is available in the selected zone.|
    |**Container Runtime**|**Docker 18.09.2**|
    |**Instance Type**|Select ecs.ebmhfg5.2xlarge of the **ECS Bare Metal instance family**.|
    |**Operating System**|**AliyunLinux 2.xxxx**|
    |**Network Plug-in**|**Flannel**|
    |**Custom Image**|Do not use **custom images**. Otherwise, confidential computing may be unavailable.|


1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes cluster**.

4.  In the Select Cluster Template dialog box, find Confidential Computing Cluster in the **Managed Clusters** section and click **Create**.

5.  On the **ACK managed edition** tab, configure the cluster.

    1.  Configure basic settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length. It can contain digits, letters, and hyphens \(-\). |
        |**Cluster Specification**|Select a cluster type. **Standard edition** and **Professional** are supported. |
        |**Region**|Select a region to deploy the ACK cluster. |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the ACK cluster belongs. The name of the selected resource group appears on the page.

![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Kubernetes Version**|Select a Kubernetes version. |
        |**Confidential Computing**|Select **Enable**.|
        |**Container Runtime**|You must select Docker.|
        |**VPC**|Select a virtual private cloud \(VPC\) to deploy the ACK cluster. Shared VPCs and standard VPCs are supported.

        -   Shared VPC: The owner of a VPC \(resource owner\) can share vSwitches in the VPC under the account of the owner with other accounts in the same organization.
        -   Standard VPC: The owner of a VPC \(resource owner\) cannot share vSwitches in the VPC under the account of the owner with other accounts.
**Note:** ACK clusters support only VPCs. You can select a VPC from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md). |
        |**VSwitch**|Select vSwitches.

You can select up to three vSwitches that are deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md). |
        |**Network Plug-in**|Confidential computing clusters support only the **Flannel** plug-in.|
        |**Pod CIDR Block**|If you select **Flannel**, you must set **Pod CIDR Block**.

The CIDR block that is specified by **Pod CIDR Block** cannot overlap with that of the VPC or existing ACK clusters in the VPC. The CIDR block cannot be modified after it is specified. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**Service CIDR**|Set **Service CIDR**. The CIDR block that is specified by **Service CIDR** cannot overlap with that of the VPC or existing ACK clusters in the VPC. The CIDR block cannot be modified after it is specified. The Service CIDR block cannot overlap with the pod CIDR block. For more information about subnetting for ACK clusters, see [Assign CIDR blocks to resources in a Kubernetes cluster under a VPC](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Assign CIDR blocks to resources in a Kubernetes cluster under a VPC.md). |
        |**IP Addresses per Node**|If you select **Flannel**, you must set the number of **IP addresses per node**.

**Note:** **IP Addresses per Node** specifies the maximum number of IP addresses that can be assigned to each node. We recommend that you use the default value. |
        |**Configure SNAT**|By default, an ACK cluster cannot be accessed over the Internet. If the VPC that you select for the ACK cluster cannot access the Internet, you can select **Configure SNAT for VPC**. Then, ACK creates a Network Address Translation \(NAT\) gateway and configures Source Network Address Translation \(SNAT\) entries to enable Internet access for the VPC. |
        |**Public Access**|By default, an internal-facing SLB instance is created for the API server. You can modify the specifications of the SLB instance. For more information, see [Specification](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server. |
        |**RDS Whitelist**|Set the Relational Database Service \(RDS\) whitelist. Add the IP addresses of nodes in the ACK cluster to the RDS whitelist.

**Note:** To enable an RDS instance to access the ACK cluster, you must deploy the RDS instance in the VPC where the ACK cluster is deployed. |
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |
        |**Secret Encryption**|Specify whether to enable **Key Management Service \(KMS\)** based on your requirements.|

    2.  Configure advanced settings of the cluster.

        |Parameter|Description|
        |---------|-----------|
        |**Kube-proxy Mode**|IPVS and iptables are supported.

        -   iptables is a kube-proxy mode. It uses iptables rules to conduct service discovery and load balancing. The performance of this mode is restricted by the size of the ACK cluster. This mode is suitable for ACK clusters that manage a small number of Services.
        -   IPVS is a high-performance kube-proxy mode. It uses Linux Virtual Server \(LVS\) to conduct service discovery and load balancing. This mode is suitable for ACK clusters that manage a large number of Services. We recommend that you use this mode in scenarios where high-performance load balancing is required. |
        |**Labels**|Add labels to nodes. Enter a key and a value, and then click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length and cannot start with http:// or https://. This parameter can be empty.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
        -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the remaining labels to take effect. |
        |**Cluster Domain**|Set the domain name of the ACK cluster.

**Note:** The default domain name is **cluster.local**. You can enter a custom domain name. A domain name consists of two parts. Each part must be 1 to 63 characters in length and can contain only letters and digits. You cannot leave these parts empty. |
        |**Custom Certificate SANs**|You can enter custom subject alternative names \(SANs\) for the API server certificate of the cluster to accept requests from specified IP addresses or domain names. |
        |**Service Account Token Volume Projection**|**Service account token volume projection** reduces security risks when pods use service accounts to access the API server. This feature enables kubelet to request and store the token on behalf of the pod. This feature also allows you to configure token properties, such as the audience and validity duration. For more information, see [Deploy service account token volume projection](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Use service account token volume projection.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the ACK cluster cannot be deleted in the console or by calling the API. This prevents user errors. |

6.  Click **Next:Worker Configurations** to configure worker nodes.

    1.  Select instances as worker nodes.

        **Note:** Only ECS Bare Metal instances can be used as worker nodes for confidential computing clusters. The instance type is ecs.ebmhfg5.2xlarge.

        -   If you select **Create Instance**, you must set the following parameters.

            |Parameter|Description|
            |---------|-----------|
            |**Billing Method**|The **pay-as-you-go** and **subscription** billing methods are supported. If you select **pay-as-you-go**, you must set the following parameters:

            -   **Duration**: You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
            -   **Auto Renewal**: Specify whether to enable auto-renewal.
The **pay-as-you-go** and **subscription** billing methods are supported.|
            |**Instance Type**|Only **ECS Bare Metal** instances support confidential computing. The instance type ecs.ebmhfg5.2xlarge supports Intel \(R\) SGX applications.|
            |**Selected Types**|The selected instance types. You can select multiple instance types.|
            |**Quantity**|Specify the number of worker nodes to be created. |
            |**System Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported.

**Note:** You can select **Enable Backup** to back up disk data. |
            |**Mount Data Disk**|**Enhanced SSDs**, **SSDs**, and **ultra disks** are supported. You can enable disk **encryption** and **backup** when you mount data disks. |
            |**Operating System**|Only the AliyunLinux 2.xxxx operating systems are supported.|
            |**Logon Type**|            -   Key pair logon.
                -   **Key Pair**: Select an SSH key pair from the drop-down list.
                -   **create a key pair**: Create an SSH key pair if no SSH key pair is available. For more information about how to create an SSH key pair, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credential that is used to log on to the ACK cluster.
            -   Password logon.
                -   **Password**: Enter the password that is used to log on to the nodes.
                -   **Confirm Password**: Enter the password again. |
            |**Key Pair**|

        -   If you select **Add Existing Instance**, you must add at least two worker nodes. You must also set **Duration**, **Auto Renewal**, **Operating System**, **Logon Type**, and **Key Pair** based on the preceding settings.
    2.  Configure advanced settings of the worker nodes

        |Parameter|Description|
        |---------|-----------|
        |**Node Protection**|Specify whether to enable node protection.

**Note:** By default, this check box is selected. The nodes in the ACK cluster cannot be deleted in the console or by calling the API. This prevents user errors. |
        |**User Data**|You can enter custom scripts. Custom scripts are automatically run after the nodes are initialized. **Note:** Nodes that run Windows support Batch and PowerShell scripts. Before you encode the content in Base64, make sure that the first line includes \[bat\] or \[powershell\]. Nodes that run Linux support shell scripts. For more information about the supported formats, see [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html) and [Prepare user data](/intl.en-US/Instance/Manage instances/User data/Prepare user data.md).

If your script file is larger than 1 KB, we recommend that you upload the script to an Object Storage Service \(OSS\) bucket and pull the script from the internal endpoint of the OSS bucket. |
        |**Custom Image**|**Note:** Do not use custom images. Otherwise, confidential computing may be unavailable. |
        |**Custom Node Name**|Specify whether to use a **custom node name**.

A node name consists of a prefix, an IP substring, and a suffix.

        -   Both the prefix and suffix can contain one or more parts that are separated with periods \(**.**\). These parts can contain lowercase letters, digits, and hyphens \(-\), and must start and end with a lowercase letter or digit.
        -   The IP substring length specifies the number of digits to be truncated from the end of the returned node IP address. Valid values: 5 to 12. |
        |**CPU Policy**|Set the CPU policy.

        -   none: This policy indicates that the default CPU affinity is used. This is the default policy.
        -   static: This policy allows pods with specific resource characteristics on the node to be granted with enhanced CPU affinity and exclusivity. |
        |**Taints**|Add taints to worker nodes in the ACK cluster. |

7.  Click **Next:Component Configurations** to configure components.

    |Parameter|Description|
    |---------|-----------|
    |**Ingress**|Specify whether to install Ingress controllers. By default, **Install Ingress Controllers** is selected. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md).

**Note:** If you want to **create Ingress dashboard**, you must first activate Log Service. |
    |**Cloud Monitor Agent**|Select whether to install the Cloud Monitor agent. After the Cloud Monitor agent is installed on ECS instances, you can view monitoring information about the instances in the Cloud Monitor console.|
    |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create a new one. By default, **Enable Log Service** is selected. When you create an application, you can perform a few steps to enable Log Service. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

You can also select or clear **Collect Logs of Control Plane Components**. If you select this check box, logs of components on the ACK control plane are collected to the Log Service project under your account. For more information, see [t2020982.md\#]().

**Note:** By default, **Collect Logs of Control Plane Components** is selected for a professional managed Kubernetes cluster. |
    |**Workflow Engine**|Specify whether to enable Alibaba Cloud Genomics Compute Service \(AGS\).

    -   If you select this check box, the system automatically installs the AGS workflow plug-in when the system creates the ACK cluster.
    -   If you clear this check box, you must manually install the AGS workflow plug-in. For more information, see [Introduction to AGS CLI](/intl.en-US/User Guide for Genomics Service/AGS workflow/Introduction to AGS CLI.md). |

8.  Click **Next:Confirm Order** to confirm the cluster configurations and select **terms of service**.

9.  Click **Create Cluster** to start the deployment.

    You can also view logs that record cluster creation events.

    **Note:** It takes about 10 minutes to create an ACK cluster that consists of multiple nodes.


-   After you create the cluster, you can view the created cluster on the Clusters page in the console.

    ![Clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6765359951/p77641.png)

-   Click **View Logs** in the **Actions** column. On the Log information page, you can view cluster logs. To view detailed log information, click **Stack events**.

    ![Cluster logs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6765359951/p77637.png)

-   On the Clusters page, find the created cluster and click **Details** in the Actions column. You can click the **Basic Information** tab to view the basic information about the cluster and click the **Connection Information** tab to view the information about how to connect to the cluster.

    The following information is displayed.

    -   **API Server Public Endpoint**: the IP address and port that the Kubernetes API Server uses to provide services over the Internet. It allows you to manage the cluster by using kubectl or other tools on the client.

        **Bind EIP** and **Unbind EIP**: These options are available to only managed Kubernetes clusters.

        -   Bind EIP: You can select an existing EIP or create one.

            The API server restarts after you bind an EIP to the API server. We recommend that you do not perform operations on the cluster during the restart process.

        -   Unbind EIP: You cannot access the API server over the Internet after you unbind the EIP.

            The API server restarts after you unbind the EIP from the API server. We recommend that you do not perform operations on the cluster during the restart process.

    -   **API Server Internal Endpoint**: the IP address and port that the Kubernetes API server uses to provide services within the cluster.
    -   **Testing Domain**: the domain name that is used for service testing. The suffix of the domain is `<cluster_id>.<region_id>.alicontainer.com`.

        **Note:** To rebind the domain name, click **Rebind Domain Name**.

-   You can use kubectl and run the `kubectl get node` command to connect to the cluster and view the information about the nodes in the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

    ![View node information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6765359951/p77634.png)


**Related topics**  


[TEE-based confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/TEE-based confidential computing.md)

[Use the Intel SGX SDK to develop and build an application](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Use the Intel SGX SDK to develop and build an application.md)

