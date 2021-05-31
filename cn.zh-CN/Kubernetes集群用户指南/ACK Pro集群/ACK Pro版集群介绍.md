---
keyword: [ACK Pro集群, Kubernetes Pro集群, Pro集群介绍]
---

# ACK Pro版集群介绍

ACK Pro托管版集群是在ACK标准托管版基础上针对企业大规模生产环境进一步增强了可靠性、安全性，并且提供可赔付的SLA的Kubernetes集群。

ACK的Pro托管版集群是在原标准ACK托管版集群的基础上发展而来的集群类型，继承了原托管版集群的所有优势，例如Master节点托管、Master节点高可用等。同时，相比原托管版进一步增强了集群的可靠性、安全性和调度性，并且支持赔付标准的SLA，适合生产环境下有着大规模业务，对稳定性和安全性有高要求的企业客户。

## 使用场景

-   互联网企业，大规模业务上线生产环境，对管控的稳定性、可观测性和安全性有较高要求。
-   大数据计算企业，大规模数据计算、高性能数据处理、高弹性需求等类型业务，对集群稳定性、性能和效率有较高要求。
-   开展中国业务的海外企业，对有赔付标准的SLA以及安全隐私等非常重视。
-   金融企业，需要提供赔付标准的SLA。

## 功能特点

-   更可靠的托管Master节点：稳定支撑大规模集群的管控；etcd容灾和备份恢复，冷热备机制最大程度保障集群数据库的可用性；管控组件的关键指标可观测，助力您更好地预知风险。
-   更安全的容器集群：管控面etcd默认采用加密盘存储；数据面通过选择安装kms-plugin组件实现Secrets数据落盘加密。开放安全管理，并提供针对运行中容器更强检测和自动修复能力的安全管理高级版。
-   更智能的容器调度：集成更强调度性能的kube-scheduler，支持多种智能调度算法，支持NPU调度，优化在大规模数据计算、高性能数据处理等业务场景下的容器调度能力。
-   SLA保障：提供赔付标准的SLA保障，集群API Server的可用性达到99.95%。

## 定价

ACK Pro版集群的计费详情，请参见[计费说明](/cn.zh-CN/产品计费/计费说明.md)。

## 对比

ACK Pro版托管版集群和ACK标准托管版集群的对比详情如下表。

|分类|功能|ACK Pro托管版集群|ACK标准托管版集群|
|--|--|------------|----------|
|集群规模|不涉及|默认配额最大1000节点。您可[到配额平台提交申请](https://quotas.console.aliyun.com/products/csk/quotas)至最大5000节点。

|最大100节点（现有集群不受影响，可以升级到Pro版）|
|SLA|不涉及|99.95%（支持赔付）|99.9%（不支持赔付）|
|API Server|自定义参数设置|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|可用性监控|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|etcd|高频冷热备机制，异地容灾|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|可观测性监控指标|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|Kube-scheduler|[Gang scheduling调度策略](/cn.zh-CN/Kubernetes集群用户指南/调度/任务调度/Gang scheduling.md)|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|[CPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/调度/CPU拓扑感知调度/CPU拓扑感知调度.md)|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|[GPU拓扑感知调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/GPU拓扑感知调度/GPU拓扑感知调度概述.md)|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|[共享GPU专业版调度](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU调度/共享GPU调度专业版/共享GPU专业版概述.md)|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|安全管理|开放高级版（支持数据加密，请参见[使用阿里云KMS进行Secret的落盘加密](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/使用阿里云KMS进行Secret的落盘加密.md)）|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|托管节点池|[托管节点池](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/托管节点池/托管节点池概述.md)|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|

**说明：**

ACK专有版集群仅支持共享GPU普通版调度。详细信息，请参见[共享GPU概述](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/共享GPU调度/共享GPU概述.md)。

