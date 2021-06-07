---
keyword: [migrate-controller, Migrate Controller组件变更记录]
---

# migrate-controller

migrate-controller是基于开源项目Velero开发的一个Kubernetes应用迁移的组件。本文为您介绍migrate-controller组件相关内容的最新动态。

## 组件介绍

有关如何安装Migrate Controller的具体步骤，请参见[安装Migrate Controller](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/应用迁移/安装Migrate Controller.md)。

Velero是一个云原生的集群应用备份、恢复和迁移工具。Velero采用GO语言编写，可以安全地备份、恢复和迁移Kubernetes集群中的应用及其持久化存储卷。有关Velero源码项目地址，请参见[velero](https://github.com/vmware-tanzu/velero)。有关Velero Plugin for Alibaba Cloud源码项目地址，请参见[velero-plugin](https://github.com/AliyunContainerService/velero-plugin)。

**说明：** Velero的运行环境要求Kubernetes集群版本大于v1.10。

## 使用说明

您可以使用Migrate Controller组件备份、恢复和迁移应用。具体操作，请参见[使用Migrate Controller备份和恢复应用](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/应用迁移/使用Migrate Controller备份和恢复应用.md)和[使用Migrate Controller跨集群迁移应用](/cn.zh-CN/Kubernetes集群用户指南/多云混合云/应用迁移/使用Migrate Controller跨集群迁移应用.md)。

## 变更记录

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.3.1-3026c10-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-3026c10-aliyun|2021年03月31日|功能升级及缺陷修复。|预计影响正在执行备份的任务，该任务可能会被中断。|
|v1.3.1-665c3ea-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.3.1-665c3ea-aliyun|2021年03月31日|注册集群支持云盘快照。|预计影响正在执行备份的任务，该任务可能会被中断。|
|v1.3.0-9274575-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2021年03月29日|增加云盘快照模式以支持卷存储备份。|预计影响正在执行备份的任务，该任务可能会被中断。如有升级需求，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术支持人员。|

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1.2-h-e616322-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020年12月24日|ACK注册集群的Velero更新到社区最新的1.5.2版本。|预计影响正在执行备份的任务，该任务可能会被中断。如有升级需求，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术支持人员。|
|v1.0.1.2-a-e616322-aliyun|registry-vpc.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.2-h-e616322-aliyun|2020年12月30日|ACK托管版集群、ACK专有版集群的Velero更新到社区最新的1.5.2版本。|预计影响正在执行备份的任务，该任务可能会被中断。如有升级需求，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)联系技术支持人员。|

**2020年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.0.1.1-30d319f-aliyun|registry.\{\{.Region\}\}.aliyuncs.com/acs/velero-installer:v1.0.1.1-30d319f-aliyun|2020年09月21日|支持ACK注册集群、ACK托管版集群应用的迁移、备份和恢复。|首次上线。|

