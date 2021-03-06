---
keyword: [共享GPU专业版, 共享GPU基础版]
---

# 共享GPU专业版概述

本文为您介绍共享GPU专业版的优势，共享GPU的基础版与专业版的功能对比，帮助您了解和更好地使用共享GPU专业版。

## 共享GPU专业版优势

|优势|说明|
|--|--|
|支持共享调度和显存隔离。|-   单Pod单GPU卡共享调度和显存隔离，常用于支持模型推理场景。
-   单Pod多GPU卡共享调度和显存隔离，常用于支持分布式模型训练代码的开发。 |
|支持共享和隔离策略的灵活配置。|-   支持按GPU卡的Binpack和Spread算法分配策略。
    -   Binpack：多个Pod会优先集中共享使用同一GPU卡，适用于需要提升GPU卡利用率的场景。
    -   Spread：多个Pod会尽量分散使用不同GPU卡，适用于GPU高可用场景。尽量避免将同一个应用的副本放置到同一个GPU设备。
-   支持只共享不隔离策略，适配于已有深度学习应用内已自建应用层隔离能力的场景。
-   同时支持多卡共享和显存隔离策略。 |
|GPU资源全方位监控。|同时支持监控独占GPU和共享GPU。|

## 共享GPU基础版和专业版对比

|功能|专业版|基础版|
|--|---|---|
|GPU单卡共享调度。|支持|支持|
|GPU多卡共享调度。|支持|不支持|
|GPU单卡隔离。|支持|支持|
|GPU多卡隔离。|支持|不支持|
|独享和共享GPU的监控和弹性伸缩。|支持|支持|
|多策略节点池。|支持，包含只共享不隔离和共享隔离策略。|支持，只共享不隔离和共享隔离策略，协同Binpack与Spread算法。|
|根据算法为Pod分配GPU显存。|支持，按Binpack和Spread算法分配节点的GPU，您可以根据实际需求选择分配算法。|支持，默认按Binpack算法分配节点的GPU。|

**说明：** 在不同的集群上安装共享GPU组件，共享GPU呈现不同的版本：

-   基础版：在专有版GPU集群安装共享GPU组件，参考文档[安装共享GPU组件](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU资源调度/共享GPU调度/安装共享GPU组件.md)
-   专业版：在ACK Pro版集群安装共享GPU组件，参考文档[安装并使用共享GPU组件和资源工具](/cn.zh-CN/Kubernetes集群用户指南/ACK Pro集群/GPU资源调度/共享GPU调度专业版/安装并使用共享GPU组件和资源工具.md)

## 阿里云共享GPU方案

在多Pod共享使用同一GPU卡时，保证显存和算力隔离是一个关键需求。如何限制运行在同一个GPU上的多个容器能够按照自己申请的资源使用量运行，避免因为其资源用量超标影响同一个GPU上的其他容器的正常工作。对此业界也做了很多探索，包括Nvidia vGPU，Nvidia MPS，rCUDA，vCUDA等社区方案，都为用户更小颗粒度地使用GPU提供了可能。

阿里云提供的共享GPU方案通过自主研发的宿主机内核驱动， 实现对NVIDIA GPU的底层nv驱动更有效的利用。共享GPU功能如下：

-   更加开放：适配开源标准的Kubernetes和NVIDIA Docker方案。
-   更加简单：优秀的用户体验。AI应用无需重编译，无需构建新的容器镜像进行CUDA库替换。
-   更加稳定：针对NVIDIA设备的底层操作更加稳定和收敛，而CUDA层的API变化多端，同时一些Cudnn非开放的API也不容易捕获。
-   完整隔离：同时支持GPU的显存和算力隔离。

阿里云提供的共享GPU方案是一套低成本、可靠、用户友好的规模化GPU调度和隔离方案，欢迎使用。

