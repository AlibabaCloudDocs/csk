---
keyword: [CCM, release notes]
---

# Cloud Controller Manager

This topic lists the latest changes to Cloud Controller Manager \(CCM\).

## November 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.335-ge5e4bc3-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.335-ge5e4bc3-aliyun|November 24, 2020|Hash values are supported in the configurations of LoadBalancer type Services. This way, when CCM is restarted, only the backend VServer groups of the related Server Load Balancer \(SLB\) instances are synchronized if the Service configurations are not changed. The configurations of the related SLB instances and listeners are not synchronized.|

## September 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.316-g8daf1a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.316-g8daf1a9-aliyun|September 29, 2020|-   The occasional failure to update the VServer groups of Server Load Balancer \(SLB\) instances is fixed.
-   The health check port is changed from port 10252 to port 10258. |

## August 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.313-g748f81e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.313-g748f81e-aliyun|August 10, 2020|-   New features:

-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection is used to set deletion protection for Server Load Balancer \(SLB\) instances. By default, deletion protection is enabled for newly created SLB instances.
-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection is used to set modification protection for the configurations of SLB instances. By default, modification protection is enabled for the configurations of newly created SLB instances.
-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id is used to specify the resource group to which an SLB instance belongs. This setting takes effect only when you create the SLB instance and cannot be modified.
-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name is used to specify the name of an SLB instance.
-   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call CCM operations, Internet access is no longer required in all regions.
-   By default, tags are added to SLB instances that are created for LoadBalancer type Services. The tags are in the `ack.aliyun.com: {your-cluster-id}` format. This feature applies to only newly created clusters of Container Service for Kubernetes \(ACK\).
-   Cloud provider ID is in the following format: `<cloudProvider>://<optional>/<segments>/<provider id>`. This feature is compatible with the community.
-   Pods are added as the backend servers of the SLB instances that are created for LoadBalancer type Services in newly created clusters that use Terway as the network plug-in. When a LoadBalancer type Service is created in a newly created ACK cluster that uses Terway, the pods that are assigned the IP addresses provided by elastic network interfaces \(ENIs\) are added as the backend servers of the related SLB instance. This improves network performance. LoadBalancer type Services do not support the targetPort field that is set to a string value.
-   Improvements:

-   Alpine Linux is upgraded to V3.11.6 for basic images.
-   Listener updates are synchronized to VServer groups.
-   SLB API operations are optimized to reduce the time that is required to create an SLB instance. |

## June 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.276-g372aa98-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.276-g372aa98-aliyun|June 11, 2020|-   New features:

-   You cannot reuse the Server Load Balancer \(SLB\) instances of the cluster API Server for LoadBalancer type Services.
-   Prometheus metrics \(ccm\_node\_latencies\_duration\_milliseconds, ccm\_route\_latencies\_duration\_milliseconds, and ccm\_slb\_latencies\_duration\_milliseconds\) are added to monitor information about the CCM synchronization delay from Services to SLB instances.
-   Events are added to monitor the synchronization process between the Service and LoadBalancer.
-   Improvements:
    -   Weight calculation is optimized for Services in Local mode. To enable the Local mode, set externalTrafficPolicy=Local in Service configurations. This improves load balancing among pods. For more information, see [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network management/FAQ.md)

    -   API calls of cloud services are optimized to improve efficiency and reduce the chances of traffic throttling.
    -   When you delete a node to which the service.beta.kubernetes.io/exclude-node label is added, the associated Ingress is no longer deleted.
-   Fixed issues:
    -   The following issue is fixed: persistence timeout cannot be set to 0 based on annotations during Service upgrades.
    -   The following issue is fixed: bandwidth cannot be set to 100 based on annotations during Service upgrades. |

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.239-g40d97e1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.239-g40d97e1-aliyun|March 5, 2020|-   New features:

For LoadBalancer type Services, CCM allows you to specify both Elastic Compute Service \(ECS\) instance-based nodes and elastic network interfaces \(ENIs\) as backend servers of Server Load Balancer \(SLB\) instances.

-   Improvements:
    -   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call CCM operations, Internet access is no longer required in regions other than China \(Beijing\), China \(Shanghai\), and UAE \(Dubai\).

    -   The API operation to query virtual private cloud \(VPC\) route entries is changed to DescribeRouteEntryList. This provides higher performance when more than 100 queries are received within a short period of time. |

## December 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.220-g24b1885-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.220-g24b1885-aliyun|December 31, 2019|-   vSwitch IDs are supported. You can set vSwitch IDs in CloudConfig by using the following format: `:vswithid1,:vswitchid2`.
-   Backoff is supported when traffic throttling is enabled. Backoff allows failed requests to rejoin the reconcile queue every 30 to 180 seconds.
-   The number of worker threads to be reconciled is adjusted to 2. This allows you to fully utilize the queries per second \(QPS\) quota on API calls to accelerate the reconcile queue.
-   The system error that is caused by concurrent Map reads and writes based on the aliyungo SDK is fixed.
-   When a node is removed from a cluster of Container Service for Kubernetes \(ACK\), CCM automatically deletes the related virtual private cloud \(VPC\) route entries from the route table.
-   The following issue is fixed: Port configurations cannot be changed due to port dependencies that are used for HTTP port forwarding.
-   If the backend server of a Server Load Balancer \(SLB\) instance is an Elastic Compute Service \(ECS\) instance, the serverip field is no longer required when you update the backend server. This avoids errors caused by different default serverip values of API requests when you add backend servers.
-   The related VPC route entries are added to the route table only when the status of a node is known.
-   CCM no longer adds Network Address Translation \(NAT\) IP addresses to node metadata. This fixes the issue when API Server cannot connect to kubelet in specific cases.
-   When you modify the configurations of a listener, the start listener operation is called only when the listener is in the inactive state. This avoids traffic throttling on API requests. |

## November 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.193-g6cddde4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.193-g6cddde4-aliyun|November 19, 2019|-   Label service.beta.kubernetes.io/exclude-node can be added to a node. In this case, CCM is no longer used to manage the node.
-   Multiple pods can be simultaneously added to the backend servers of an SLB instance. The network type of the pods must be Terway.
-   The node weight cannot be less than 1 for Services in Local mode \(when externalTrafficPolicy=Local is set for Services\).
-   The following issue is fixed: VServer groups are repeatedly created when concurrent requests are processed.
-   The following issue is fixed: Dirty data is generated due to caching when you set node weights. |

## September 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.164-g2105d2e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3-164-g2105d2e-aliyun|September 11, 2019|-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id is used to update certificates.
-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port is used to enable port forwarding from an HTTP port to an HTTPS port.
-   The following annotations are used to create Server Load Balancer \(SLB\) instances with access control list \(ACL\) settings: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id, and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type.
-   Annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend is used to remove unschedulable nodes.
-   Annotation service.beta.kubernetes.io/backend-type: "eni" is used to mount pods in Terway mode as the backend servers of an SLB instance. This improves network forwarding performance.
-   Services in Local mode \(externalTrafficPolicy=Local is set for the Services\) can automatically set node weights based on the number of pods on each node. |

## April 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.105-gfd4e547-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun|April 15, 2019|-   Multiple route tables can be created in a virtual private cloud \(VPC\). The configuration file can be used to set multiple route tables for a cluster.
-   The following issue is fixed: Updated HTTP configurations do not take effect. |

## March 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.81-gca19cd4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun|March 20, 2019|-   Existing Server Load Balancer \(SLB\) instances can be reused by managed and dedicated Kubernetes clusters. These SLB instances are not created by Container Service for Kubernetes \(ACK\).
-   Custom node names are supported. Node naming no longer depends on the nodeName field in Kubernetes.
-   The compatibility issue between CCM 1.8.4 and Kubernetes 1.11.5 is fixed. We recommend that you upgrade CCM to the latest version. |

## December 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.59-ge3bc999-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun|December 26, 2018|-   Multiple Kubernetes Services can share the same Server Load Balancer \(SLB\) instance.
    -   If an SLB instance is created when you create a Service, you cannot use this SLB instance when you create other Services. Otherwise, the SLB instance may be deleted. You can reuse only the SLB instances that are manually created in the console or by calling the API.
    -   Services that share the same SLB instance cannot use the same frontend listening port. Otherwise, port conflicts may occur.
    -   When you reuse an SLB instance, you must use the listener name and VServer group name as identifiers. Do not modify the listener name or VServer group name.
    -   You can modify the name of the SLB instance.
    -   You cannot reuse SLB instances across clusters.
-   The traffic throttling issue is fixed when virtual private cloud \(VPC\) route tables are managed in sequence, instead of being managed in parallel. |

## August 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.10-gfb99107-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun|August 15, 2018|-   Annotation service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid is used to specify the primary zone for automatically created Server Load Balancer \(SLB\) instances.
-   Annotation service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid is used to specify the secondary zone for automatically created SLB instances.

**Note:** This parameter does not take effect in regions that do not support SLB instances that are deployed across primary and secondary zones.

-   Annotation service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners is used to overwrite existing listeners when you reuse an SLB instance.
-   Annotation service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth is used to specify the bandwidth when you create a pay-by-bandwidth SLB instance. The bandwidth is shared among listeners of the SLB instance. |

## June 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3|June 25, 2018|-   Annotation service.beta.kubernetes.io/alicloud-loadbalancer-backend-label is used to add worker nodes with specific labels as the backend servers of Server Load Balancer \(SLB\) instances.
-   Annotation service.beta.kubernetes.io/alicloud-loadbalancer-spec is used to specify the SLB instance types, such as the shared-performance or exclusive instance types.
-   `externalTrafic: Local` is used to set the Local mode for Services. When this mode is enabled, only nodes that host pods can be added as the backend servers of the SLB instance that is attached to the cluster.
-   When a node is added to or removed from a cluster, the system automatically adds the node to or removes it from the backend servers of the SLB instance that is attached to the cluster.
-   When the label of a node is changed, the system automatically adds the node to or removes it from the backend servers of the SLB instance that is attached to the cluster.
-   Sticky sessions are supported.
-   When you create a Service based on an existing SLB instance, the system no longer manages listeners. You must manually add listeners. |

