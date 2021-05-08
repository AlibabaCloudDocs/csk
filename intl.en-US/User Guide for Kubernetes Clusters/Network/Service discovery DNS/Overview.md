---
keyword: [CoreDNS, service discovery, DNS]
---

# Overview

CoreDNS is deployed in Container Service for Kubernetes \(ACK\) clusters and serves as a Domain Name System \(DNS\) server to provide DNS resolution services for workloads deployed in ACK clusters. This topic introduces CoreDNS and its features, and describes how CoreDNS resolves domain names in Kubernetes clusters.

## Introduction to CoreDNS

CoreDNS is a DNS resolver for Kubernetes clusters. CoreDNS resolves custom internal domain names and external domain names. CoreDNS contains a variety of plug-ins. These plug-ins allow you to configure custom DNS settings and customize hosts, Canonical Name \(CNAME\) records, and rewrite rules for Kubernetes clusters. CoreDNS is hosted by [Cloud Native Computing Foundation \(CNCF\)](https://cncf.io/), which also hosts Kubernetes. For more information about CoreDNS, see [CoreDNS: DNS and Service Discovery](https://coredns.io/).

ACK uses CoreDNS to implement service discovery in clusters. You can configure CoreDNS based on your requirements to support higher numbers of DNS queries in different scenarios.

-   For more information about CoreDNS configurations in ACK clusters, see [Configure CoreDNS for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md).
-   For more information about how to use CoreDNS to improve the performance of DNS resolutions for an ACK cluster, see [Optimize DNS resolution for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Optimize DNS resolution for an ACK cluster.md).

**Note:**

You can also use NodeLocal DNSCache to improve the stability and performance of service discovery in ACK clusters. NodeLocal DNSCache improves DNS performance by running a DNS caching agent on nodes as a DaemonSet. For more information about how to deploy NodeLocal DNSCache in an ACK cluster, see [Deploy Node Local DNS in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Deploy Node Local DNS in an ACK cluster.md).

## Mappings between CoreDNS versions and Kubernetes versions

ACK maintains a mapping between CoreDNS versions and Kubernetes versions. You can upgrade CoreDNS versions only when you upgrade Kubernetes versions for ACK clusters. The following table describes the mapping between CoreDNS versions and Kubernetes versions.

**Note:** To view the current CoreDNS version of your cluster, log on to the ACK console, go to the **Pods** page, and then check the image version of the pod named coredns-xxx. For more information, see [Manage pods](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Manage pods.md).

|Item|Version|
|----|-------|
|Kubernetes|1.12|1.14|1.16|1.18|
|CoreDNS|1.2.6|1.3.1|1.6.2|1.6.7|

## DNS resolutions in an ACK cluster

The startup parameters of kubelet in an ACK cluster include `--cluster-dns=<dns-service-ip>` and `--cluster-domain=<default-local-domain>`. These parameters are used to configure the IP address and the suffix of the base domain name for the DNS server in the ACK cluster.

By default, ACK deploys a set of workloads in a cluster to run CoreDNS. A Service named **kube-dns** is deployed to expose these workloads to DNS queries in the cluster. Two pods named **coredns** are deployed as the backend of CoreDNS. DNS queries in the cluster are sent to the DNS server that is specified in the coredns pod configurations. The DNS configuration file in the pod is /etc/resolv.conf. The file contains the following content:

```
nameserver 172.xx.x.xx
search kube-system.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

![How a kube-dns pod resolves domain names](../images/p232986.png "The following figure shows how domain names are resolved in an ACK cluster.")

|No.|Description|
|---|-----------|
|①|When a client pod attempts to access Service Nginx, the pod sends a request to the DNS server that is specified in the DNS configuration file /etc/resolv.conf. In this example, the DNS server address is 172.21.0.10, which is the IP address of Service kube-dns. The result of the resolution is 172.21.0.30.|
|②|The client pod sends another request to 172.21.0.30, which is the IP address of Service Nginx. Then, the request is forwarded to the backend pods Nginx-1 and Nginx-2.|

For more information about DNS resolutions in Kubernetes clusters, see [t1985778.md\#section\_gof\_bp4\_37b](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md).

**Related topics**  


[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)

[Optimize DNS resolution for an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Optimize DNS resolution for an ACK cluster.md)

[Deploy Node Local DNS in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Deploy Node Local DNS in an ACK cluster.md)

