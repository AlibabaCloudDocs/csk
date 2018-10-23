# 基于Helm的发布管理 {#concept_tcp_sb4_vdb .concept}

在阿里云 Kubernetes 服务提供了丰富的云市场，其中的应用目录和服务目录功能集成了 helm 包管理工具，帮助您快速构建云上应用，因为 chart 可以多次发布（release），这就带来一个发布版本管理的问题。因此，阿里云 Kubernetes 服务提供了发布功能，通过 Web 界面的方式对通过 helm 发布的应用进行管理。

## 前提条件 {#section_ldn_dc4_vdb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#)。
-   您已经使用应用目录或服务目录功能，安装了 helm 应用，参见[利用 Helm 简化应用部署](cn.zh-CN/用户指南/Kubernetes 集群/应用管理/利用 Helm 简化应用部署.md#)。本例中是一个 tf-model 应用。

## 查看发布的详情 {#section_xsk_fc4_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **发布**，选择所需的集群，进入发布列表页面。

    您可看到该集群下通过 helm 包管理工具发布的应用及服务等。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734575_zh-CN.png)

3.  以 tf-model 为例，您可查看发布的详情信息，单击右侧的**详情**，进入该发布的详情页面。

    你可以查看该发布的当前版本和历史版本等信息，当前版本为1，无历史版本。您还可查看 tf-model 的资源信息，如资源名称，资源类型，以及查看 YMAL 信息。

    **Note:** 单击资源名称，可进入 Kubernetes Dashboard 页面，查看对应资源的详细运行状态。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734576_zh-CN.png)

4.  单击**参数**，你可查看该 helm 包安装的参数配置。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734577_zh-CN.png)


## 更新发布的版本 {#section_qmz_gc4_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **发布**，选择所需的集群，进入发布列表页面。

    您可看到该集群下通过 helm 包管理工具发布的应用及服务等。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734575_zh-CN.png)

3.  以 tf-model 为例，您可更新该发布，单击右侧的**更新**，弹出更新发布对话框。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734579_zh-CN.png)

4.  在对话框中修改相关参数，随后单击**更新**，可对该发布进行更新。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734580_zh-CN.png)

    在发布列表页面，您可以看到当前版本变为 2，您可以在历史版本菜单下找到版本1，单击 **回滚到该版本**，可进行回滚。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865744582_zh-CN.png)


## 删除发布 {#section_nsk_fc4_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **发布**，选择所需的集群，进入发布列表页面。

    您可看到该集群下通过 helm 包管理工具发布的应用及服务等。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865734575_zh-CN.png)

3.  以 tf-model 为例，您可删除该发布，单击右侧的**删除**，弹出删除对话框。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6918/15395865744583_zh-CN.png)

4.  勾选是否**清除发布记录**，然后单击**确定**，您可以删除 tf-model 应用，其包含的 service、deployment 等资源都会一并删除。

