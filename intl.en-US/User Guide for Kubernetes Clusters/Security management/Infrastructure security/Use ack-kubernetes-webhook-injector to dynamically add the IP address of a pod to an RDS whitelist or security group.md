---
keyword: [K8s webhook injector, add security group rule, IP address, ack-kubernetes-webhook-injector]
---

# Use ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to an RDS whitelist or security group

In some scenarios where fine-grained permission control is required, you may need to dynamically add the IP addresses of pods to security groups or Relational Database Service \(RDS\) whitelists. You may also need to remove these IP addresses from security groups or RDS whitelists. You can use ack-kubernetes-webhook-injector to perform these operations. This requires you to add annotations to pod configurations. This topic describes how to install and use ack-kubernetes-webhook-injector.

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md)

In cloud computing scenarios, you must configure security policies to allow external access to some cloud resources. For example, you must add inbound rules to a security group to allow external access to the Elastic Compute Service \(ECS\) instances in the security group. You must configure an RDS whitelist to allow access from specified client IP addresses. When you create a Container Service for Kubernetes \(ACK\) cluster, you can add the CIDR block of the cluster nodes to an RDS whitelist. However, the following limits apply:

-   The whitelist controls access in a coarse-grained manner because the IP addresses of all nodes and pods are added to the whitelist.
-   The whitelist is not automatically deleted after the cluster is deleted. You must manually delete the whitelist.
-   You cannot add inbound rules to the security group to which the cluster nodes belong when you create the cluster.

To fix the preceding issues, ack-kubernetes-webhook-injector is developed by ACK to provide fine-grained security control over cloud resources. You can use ack-kubernetes-webhook-injector to dynamically add the IP address of a pod to an RDS whitelist or security group. When the pod is deleted, the IP address is automatically removed from the RDS whitelist or security group.

The following features are provided by ack-kubernetes-webhook-injector:

-   When a pod is created or deleted, the IP address of the pod is automatically added to or removed from a specified security group.
-   When a pod is created or deleted, the IP address of the pod is automatically added to or removed from a specified RDS whitelist.

## Install ack-kubernetes-webhook-injector

Before you use ack-kubernetes-webhook-injector, you must install the component. Perform the following steps:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  Select and click **ack-kubernetes-webhook-injector**.

4.  On the **Parameters** tab, set `use_aksk` to `true`, and specify the AccessKey pair of the current account or . For more information, see [Obtain an AccessKey pair]().

    ![AK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6523149061/p179747.png)

5.  On the right side of the page, select a cluster to install the component and click **Create**.


## Examples on how to use ack-kubernetes-webhook-injector

You only need to add an annotation to the Pod Spec parameter of the replication controller for a pod. The annotation must specify the ID of an RDS whitelist or security group. This way, when the pod is created, the IP address of the pod is automatically added to the specified RDS whitelist or security group. When the pod is deleted, the IP address of the pod is automatically removed from the specified RDS whitelist or security group.

The following annotations are supported:

-   Annotations related to the RDS whitelist:
    -   The ID of the RDS instance: ack.aliyun.com/rds\_id.
    -   The name of the RDS whitelist: ack.aliyun.com/white\_list\_name.
-   The annotation related to the security group: ack.aliyun.com/security\_group\_id

In the following example, an RDS whitelist is used to show how to dynamically add the IP address of a pod to the RDS whitelist by using ack-kubernetes-webhook-injector.

1.  Create a Deployment and add an annotation in the pod configuration to specify an RDS instance ID and add another annotation to specify an RDS whitelist. The following YAML template is provided as an example:

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: inject-test
      name: inject-test
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: inject-test
      template:
        metadata:
          annotations:
            ack.aliyun.com/rds_id: <rm-wz9nanjcud75bxxxx>
            ack.aliyun.com/white_list_name: <rds_group>
          labels:
            app: inject-test
        spec:
          containers:
          - command:
            - sleep
            - "3600"
            image: alpine:latest
            name: inject-test
    ```

2.  Run the following command to query the IP address of the application pod:

    ```
    kubectl --kubeconfig .kube/config_sts_test -n inject-test get pod -o wide
    ```

    The following output is returned:

    ```
    NAME                           READY           STATS        RESTARTS    AGE    IP                NODE
    inject-test-68cc8f9bbf-gj86n    1/1            Running         0        22s   172.25.0.28    cn-hangzhou.xxx
    ```

    The output shows that the IP address of the pod is 172.25.0.28.

3.  Log on to the [RDS console](https://rdsnext.console.aliyun.com/?spm=5176.2020520152.nav-right.2.469016ddzrU6KW#/detail/rm-bp12685y16w4zjz9d/security/whiteList?region=cn-hangzhou) and check the whitelist of the specified RDS instance. For more information about how to check an RDS whitelist, see [Configure an enhanced IP address whitelist](/intl.en-US/RDS PostgreSQL Database/Quick start/Set the whitelist/Configure an IP address whitelist for an ApsaraDB RDS for PostgreSQL instance.md).

4.  Change the number of replicated pods to 0 for the Deployment that is created in Step 1. Then, log on to the RDS console and check the whitelist of the specified RDS instance.

    You can find that the IP address is removed from the RDS whitelist.

    **Note:** You can perform similar steps to add the IP address of a pod to a security group by using ack-kubernetes-webhook-injector.


## Uninstall ack-kubernetes-webhook-injector

If you no longer use ack-kubernetes-webhook-injector, you can uninstall ack-kubernetes-webhook-injector by using the release feature that is provided by ACK. For more information, see [Delete a release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage releases by using Helm.md). To delete the related configurations, run the following commands:

```
kubectl -n kube-system delete secret kubernetes-webhook-injector-certs
kubectl delete mutatingwebhookconfigurations.admissionregistration.k8s.io kubernetes-webhook-injector
```

