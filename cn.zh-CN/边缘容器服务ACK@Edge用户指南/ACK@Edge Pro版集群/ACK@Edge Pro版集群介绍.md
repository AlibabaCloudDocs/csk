---
keyword: ACK@Edge Pro集群
---

# ACK@Edge Pro版集群介绍

ACK@Edge Pro版集群针对企业大规模生产环境，在ACK@Edge标准版基础上进一步增强了可靠性、安全性，并且提供可赔付的SLA的Edge Pro Kubernetes集群。

ACK@Edge Pro版集群是在原ACK@Edge标准版集群的基础上发展而来的集群类型，继承了原边缘标准版集群的所有优势，例如Master节点托管、Master节点高可用等。同时，相比原边缘标准版进一步增强了集群的可靠性、安全性和调度性，并且支持赔付标准的SLA，适合生产环境下有着大规模业务，对稳定性和安全性有高要求的企业客户。

## 使用场景

-   互联网企业，大规模业务上线生产环境，对管控的稳定性、可观测性和安全性有较高要求。
-   大数据计算企业，大规模数据计算、高性能数据处理、高弹性需求等类型业务，对集群稳定性、性能和效率有较高要求。
-   开展中国业务的海外企业，对有赔付标准的SLA以及安全隐私等非常重视。
-   金融企业，需要提供赔付标准的SLA。

## 功能特点

-   更可靠的托管Master节点：ETCD容灾和备份恢复，冷热备机制最大程度保障集群数据库的可用性；管控组件的关键指标可观测，助力您更好地预知风险。
-   更安全的容器集群：管控面ETCD默认采用加密盘存储；数据面通过安装kms-plugin组件实现Secrets数据落盘加密。开放安全管理，并提供针对运行中容器更强检测和自动修复能力的安全管理高级版。
-   更智能的容器调度：集成更强调度性能的kube-scheduler，支持本地存储LVM调度，支持多种智能调度算法，支持NPU调度，优化在大规模数据计算、高性能数据处理等业务场景下的容器调度能力。
-   SLA保障：提供赔付标准的SLA保障，集群API Server的可用性达到99.95%。

## 定价

ACK@Edge Pro版集群的计费详情，请参见[ACK@Edge计费说明](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/ACK@Edge计费说明.md)。

## 对比

ACK@Edge Pro版集群和ACK@Edge标准版集群的对比详情如下表。

|分类|功能|ACK@Edge|
|ACK@Edge Pro版集群|ACK@Edge标准版集群|
|--|--|--------|
|---------------|-------------|
|集群规模|不涉及|最大1000节点|最大100节点（现有集群不受影响，可以升级到Pro版）|
|SLA|不涉及|99.95%（支持赔付）|99.9%（不支持赔付）|
|API Server|自定义参数设置机制|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|可用性监控|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|ETCD|高频冷热备机制，异地容灾|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|可观测性监控指标|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|
|安全管理|开放高级版（支持数据加密，请参见[使用阿里云KMS进行Secret的落盘加密](/cn.zh-CN/边缘容器服务ACK@Edge用户指南/ACK@Edge Pro版集群/使用阿里云KMS进行Secret的落盘加密.md)）|![支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9668261161/p232205.png)|![不支持](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8180361161/p232208.png)|

## 开服地域

-   亚太

    |地域名称|所在城市|Region ID|
    |----|----|---------|
    |华北2|北京|cn-beijing|
    |华北3|张家口|cn-zhangjiakou|
    |华北5|呼和浩特|cn-huhehaote|
    |华北6|乌兰察布|cn-wulanchabu|
    |华东1|杭州|cn-hangzhou|
    |华东2|上海|cn-shanghai|
    |华南1|深圳|cn-shenzhen|
    |华南2|河源|cn-heyuan|
    |西南1|成都|cn-chengdu|
    |中国（香港）|中国香港|cn-hongkong|
    |日本|东京|ap-northeast-1|
    |新加坡|新加坡|ap-southeast-1|
    |澳大利亚|悉尼|ap-southeast-2|
    |马来西亚|吉隆坡|ap-southeast-3|
    |印度尼西亚|雅加达|ap-southeast-5|

-   美洲与欧洲

    |地域名称|所在城市|Region ID|
    |----|----|---------|
    |美国西部|硅谷|us-west-1|
    |美国东部|弗吉尼亚|us-east-1|
    |英国|伦敦|eu-west-1|
    |德国|法兰克福|eu-central-1|

-   印度

    |地域名称|所在城市|Region ID|
    |----|----|---------|
    |印度|孟买|ap-south-1|


