# Limits {#concept_gsf_w2b_5db .concept}

The limits for Alibaba Cloud Container Service Kubernetes clusters are as follows.

## Cluster {#section_ldw_hfb_5db .section}

-   By default, you can create a maximum of five clusters \(in all regions\) and add a maximum of 20 nodes to each cluster. To create more clusters or add more nodes to a cluster, open a ticket.
-   Currently, Kubernetes clusters only support Linux containers. The support for Windows containers is under development.
-   Currently, Kubernetes clusters only support Virtual Private Cloud \(VPC\).  You can select to create a VPC or use an existing VPC when creating a Kubernetes cluster.
-   Currently, all the created cluster resources, such as Elastic Compute Service \(ECS\) instances and Server Load Balancer instances, are Pay-As-You-Go resources. You can change the Pay-As-You-Go instances to monthly or yearly subscription instances in the console.

## ECS instances {#section_fth_hfb_5db .section}

-   Only support the CentOS operating system.
-   Limits for adding an existing node:
    -   The ECS instance to be added must be in the same region and VPC as the cluster.
    -   When adding an existing ECS instance, make sure that your ECS instance has an Elastic IP \(EIP\) for the network type VPC. Otherwise, the ECS instance will fail to be added.
    -   The ECS instance to be added must be under the same account as the cluster.

## Cluster expansion and contraction {#section_cnt_pfb_5db .section}

-   The number of worker nodes must be from 1 to 37.
-   Currently, you can only expand or contract a cluster manually. Auto scaling is not supported.
-   Currently, master nodes in a Kubernetes cluster cannot be expanded automatically.
-   According to the Resource Orchestration Service \(ROS\) rules, elastic contraction will not contract the nodes created when the cluster is created or the nodes manually added to the cluster, but will only contract the nodes manually expanded by you.  The contraction rule is based on the creation time. The later the node is added by expansion, the sooner the node is recycled.
-   The nodes added by cluster expansion are Pay-As-You-Go nodes.

