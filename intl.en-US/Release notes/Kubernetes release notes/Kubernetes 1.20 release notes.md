---
keyword: [Kubernetes, 1.20, ACK, release notes]
---

# Kubernetes 1.20 release notes

Container Service for Kubernetes \(ACK\) strictly abides by the terms of the Certified Kubernetes Conformance Program. This topic lists the changes that ACK has made to support Kubernetes 1.20.

## Version upgrade

All ACK components have been upgraded and optimized to support Kubernetes 1.20.

|Core component|Version|Upgrade notes|
|--------------|-------|-------------|
|Kubernetes|1.20.4|-   Before you upgrade an ACK cluster to Kubernetes 1.20 or later, make sure that the required subject alternative names \(SANs\) are included in the self-signed server certificates of the admission webhooks in the cluster. For more information, see the [sample Helm chart](https://github.com/helm/helm/issues/9046#issuecomment-750892734).
-   The selfLink field is deprecated. For more information, see [Stop setting SelfLink in kube-apiserver](https://github.com/kubernetes/kubernetes/pull/94397).
-   By default, the `node-role.kubernetes.io/control-plane` label is added by ACK to the master nodes of a dedicated Kubernetes cluster. The `node-role.kubernetes.io/master` label is deprecated in Kubernetes versions later than 1.20. |
|Docker Runtime|19.03.5|None|
|Containerd Runtime|1.4.4|None|
|etcd|3.4.3|None|
|CoreDNS|1.7.0|-   The deprecated upstream plug-in is no longer compatible. If the upstream plug-in is specified in Corefile configurations, it will be automatically deleted in a secure way when CoreDNS is upgraded.
-   The names of metrics are updated. If your monitoring system is dependent on CoreDNS metrics, you must update the metric names. For more information, see [Metric changes](https://coredns.io/2020/06/15/coredns-1.7.0-release/#metric-changes). |
|NVIDIA Container Runtime|3.4.1|None|

## Version details

**Resource changes and deprecation**

-   The Docker runtime is deprecated. The Docker runtime is marked as deprecated in Kubernetes 1.20. However, you can continue using the Docker runtime in your clusters. The Docker runtime will not be supported by open source Kubernetes in later versions. This change does not affect container images. You can still build Docker images. For more information, see [Dockershim Deprecation FAQ](https://kubernetes.io/blog/2020/12/02/dockershim-faq/).
-   By default, the `node-role.kubernetes.io/control-plane` label is added by ACK to the master nodes of a dedicated Kubernetes cluster. The `node-role.kubernetes.io/master` label is deprecated in Kubernetes versions later than 1.20.
-   The selfLink field is deprecated. For more information, see [Stop setting SelfLink in kube-apiserver](https://github.com/kubernetes/kubernetes/pull/94397).
-   The `extensions/v1beta1` and `networking.k8s.io/v1beta1` API versions are no longer used to manage Ingresses and IngressClasses, and will be deprecated in Kubernetes versions later than V1.22. Use `networking.k8s.io/v1` instead.

    **Note:** By default, the NGINX Ingress controller is installed in ACK cluster. This component enables you to use the networking.k8s.io/v1beta1 API version to manage Ingresses and IngressClasses.

-   The required SANs must be included in the self-signed server certificates of the admission webhooks in ACK clusters. Before you upgrade an ACK cluster to Kubernetes 1.20 or later, make sure that the required SANs are included in the self-signed server certificates of the admission webhooks in the cluster. For more information, see the [sample Helm chart](https://github.com/helm/helm/issues/9046#issuecomment-750892734).

**Feature enhancements**

-   The issue that exec probes do not time out based on the timeout settings is fixed for kubelet. The default timeout period for exec probes is now 1 second, which may be short for some exec probes. If the timeout period is not specified for exec probes, we recommend that you specify the default timeout period.
-   The API Priority and Fairness feature \(APF\) is a feature of Kubernetes in public preview and is enabled by default. You can use this feature to limit and prioritize requests. For more information, see [API Priority and Fairness](https://kubernetes.io/docs/concepts/cluster-administration/flow-control/).
-   By default, the EndpointSlice feature is enabled. In Kubernetes 1.19 and later, the EndpointSlice feature is automatically enabled by kube-proxy to support large-scale clusters. For more information, see [EndpointSlices](https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/).
-   Immutable ConfigMaps and Secrets are supported. The immutable ConfigMaps and Secrets feature is in public preview. If a ConfigMap or Secret is set to immutable, it cannot be modified. This reduces the load on kube-apiserver. For more information, see [Immutable ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/#configmap-immutable).

## Enhancements to Kubernetes 1.20

**Control plane improvements**

-   Observability. Metrics are collected to monitor request operations and watch operations. This improves the observabilities of control plane components.
-   Stability. Protection is provided to defend etcd against excessive requests when a cluster is started. This improves system stability.
-   Performance optimizations. Indexes are added to accelerate the processing of list requests. This reduces the CPU usage of kube-apiserver.

**Enhancements of Windows containers**

-   By default, the EndpointSlice feature is enabled.
-   Device plug-ins are supported. For more information, see [Device plug-ins](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/?spm=a2c4e.11153959.blogcont498185.15.3ae63614Bg2zWO).

## References

-   [CHANGELOG-1.20.md](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.20.md)
-   [CHANGELOG-1.19.md](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.19.md)

