---
keyword: [边缘托管集群, 边缘计算, edge, 边缘网络, 网络自治, 边缘运维通道]
---

# ACK@Edge概述

阿里云边缘容器服务ACK@Edge是阿里云容器服务针对边缘计算场景推出的云边一体化协同托管方案。本文介绍阿里云边缘托管Kubernetes集群的产生背景和主要功能。

## 产品简介

随着互联网智能终端设备数量的急剧增加，以及5G和物联网时代的到来，传统云计算中心集中存储、计算的模式已经无法满足终端设备对于时效、容量、算力的需求，将云计算的能力下沉到边缘侧、设备侧，并通过中心进行统一交付、运维、管控，将是云计算的重要发展趋势。

阿里云边缘托管Kubernetes集群在云端提供一个标准、安全、高可用的Kubernetes集群，整合阿里云虚拟化、存储、网络和安全等能力，并简化集群运维工作，让您专注于容器化应用的开发与管理。ACK@Edge具有以下特点：

-   支持云端托管，帮助您快速构建边缘计算的云原生基础设施。
-   支持多种边缘计算资源的快速接入，包括IoT网关设备、端设备、CDN资源、自建IDC资源等。
-   支持X86和ARM架构。
-   支持丰富的应用场景，包括边缘智能、智慧楼宇、智慧工厂、音视频直播、在线教育、CDN等。

阿里云边缘托管Kubernetes集群，采用非侵入方式增强，提供边缘自治、边缘单元、边缘流量管理、原生运维API支持等能力，以原生方式支持边缘计算场景下的应用统一生命周期管理和统一资源调度。



## 功能介绍

![边缘集群架构图](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7325449951/p99748.png)

阿里云边缘托管Kubernetes集群支持对边缘计算场景的容器应用和资源全生命周期管理，具有以下功能：

-   通过控制台一键创建高可用的边缘Kubernetes集群，并提供集群的扩容、升级、日志、监控等生命周期管理运维能力。
-   支持丰富的异构边缘节点资源，包括自建IDC资源、[ENS](https://www.aliyun.com/product/ens)、IoT设备、X86、ARM架构等；并支持异构资源的混合调度。
-   面向边缘计算弱网络连接场景，提供节点自治和网络自治能力，保证边缘节点和边缘业务的高可靠运行。
-   提供反向运维网络通道能力。
-   提供边缘单元管理、单元化部署、单元流量管理能力。

## ACK@Edge交流群

如果您对于ACK@Edge有任何疑问，欢迎使用钉钉扫描二维码或者搜索群号（21976595）加入钉钉交流群。

![二维码](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8325449951/p146787.png)

**相关文档**  


[创建边缘托管版集群](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/边缘托管集群管理/创建边缘托管版集群.md)

[升级边缘集群](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/边缘托管集群管理/升级边缘集群.md)

[扩容边缘集群](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/边缘托管集群管理/扩容边缘集群.md)

