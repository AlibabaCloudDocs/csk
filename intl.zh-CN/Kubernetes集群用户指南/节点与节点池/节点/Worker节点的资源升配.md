---
keyword: [Worker节点, 资源升配]
---

# Worker节点的资源升配

随着Kubernetes集群负载的增加，用户常常面临资源不足的情况，此时您可以对集群的基础资源做一些扩展。例如，当集群的节点数达到10个以上后，您可以优先考虑升级Worker节点的配置，这样既可以提高资源的利用率，也可以降低集群运维的复杂度。本文为您介绍如何升级Worker节点的配置。

已创建Kubernetes集群。具体操作，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  单击目标Worker节点的实例ID，进入实例详情页面（本文以worker-k8s-for-cs实例为例）。

    此时，您可以看到目标节点的实例规格等信息。

    本例仅介绍了按量付费类型的Worker节点的升配方式，关于更多类型升配方式，请参见[升降配方式概述](/intl.zh-CN/实例/升降配实例/升降配方式概述.md)。

6.  若**实例规格**右侧的**更改实例规格**为灰显，说明实例正在运行 ，您需要单击界面的**停止**。

    ![实例详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9221465261/p50527.png)

    在停止实例对话框选中**停止方式**及**停止模式**后，单击**确定**。

7.  在**实例规格**右侧，单击**更改实例规格**。

    ![实例规格变更](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1321465261/p292164.png)

8.  在更改实例规格页面，选择合适的实例规格，单击**立即变更**。

    关于节点实例规格，请参见[选择Worker节点规格](/intl.zh-CN/最佳实践/集群/ECS选型.md)。

9.  在调整规格成功对话框，单击**管理控制台**。

    此时自动跳转到实例页面。

    ![实例列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3727465261/p50514.png)

    可以看到worker-k8s-for-cs节点的规格已经调整成功。

10. 在目标实例右侧**操作**列，选择**更多** \> **实例状态** \> **启动**，在启动实例对话框单击**确定**后，等待升配的worker-k8s-for-cs节点自动加入集群且状态变成**运行中**即可完成Worker节点的资源升配。


