---
keyword: ECI GPU容器实例
---

# 使用ECI GPU容器实例

本文以在Serverless kubernetes集群中使用Tensorflow进行图片识别为例，说明如何使用GPU容器实例。

您已经创建一个ASK集群，或已在Kubernetes集群创建一个虚拟节点。具体操作，请参见[Serverless Kubernetes集群](/intl.zh-CN/Serverless Kubernetes集群用户指南/快速入门/创建Serverless Kubernetes集群.md)或[通过部署ACK虚拟节点组件创建ECI Pod](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

ASK基于ECI（弹性容器实例）正式推出GPU容器实例支持，让您以Serverless的方式快速运行AI计算任务，极大降低AI平台运维的负担，显著提升整体计算效率。

AI计算离不开GPU已经是行业共识，然而从零开始搭建GPU集群环境是件相对复杂的任务，包括GPU规格购买、机器准备、驱动安装、容器环境安装等。GPU资源的Serverless交付方式，充分的展现了Serverless的核心优势，其向您提供标准化而且“开箱即用”的资源供给能力，您无需购买机器也无需登录到节点安装GPU驱动，极大降低了AI平台的部署复杂度，让客户关注在AI模型和应用本身而非基础设施的搭建和维护，让使用 GPU/CPU资源就如同打开水龙头一样简单方便，同时按需计费的方式让客户按照计算任务进行消费，避免包年包月带来的高成本和资源浪费。

在容器服务ACK Serverless中创建挂载GPU的Pod，通过Annotation指定所需GPU的类型，同时在`resource.limits`中指定GPU的个数即可（也可指定instance-type）。每个Pod独占GPU，GPU实例的收费与ECS GPU类型收费一致，不产生额外费用。

**说明：** 创建挂载实例类型为vGPU的Pod暂不支持该功能。

## 操作步骤

本文以在Serverless kubernetes集群中使用Tensorflow进行图片识别为例，说明如何使用GPU容器实例。

![image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3748649951/p47460.png)

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

5.  在**无状态**页面单击**使用YAML创建资源**，选择示例模板或自定义，然后单击**创建**。

    您可以使用以下YAML示例模板创建Pod（本例中，Pod中指定GPU类型为 P4，GPU个数为 1） 。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: tensorflow
      annotations:
        k8s.aliyun.com/eci-gpu-type : "P4"
    spec:
      containers:
      - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
        name: tensorflow
        command:
        - "sh"
        - "-c"
        - "python models/tutorials/image/imagenet/classify_image.py"
        resources:
          limits:
            nvidia.com/gpu: "1"
      restartPolicy: OnFailure
    ```

6.  在集群管理页左侧导航栏中，选择**工作负载** \> **容器组**，查看容器组的状态。

7.  单击目标容器组，单击容器组 - tensorflow中的**日志**页签，可以看到以下红框内容时，表示图片识别成功。

    ![容器组 - tensorflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3748649951/p47463.png)


如果您想要在容器服务ACK的虚拟节点中使用该功能，请参见[通过部署ACK虚拟节点组件创建ECI Pod](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)，需要把Pod指定调度到虚拟节点上，或把Pod创建在有virtual-node-affinity-injection=enabled label 的Namespace中，然后使用以下示例文件替换步骤[5](#step_a0i_3au_ds9)的YAML文件即可。

```
apiVersion: v1
kind: Pod
metadata:
  name: tensorflow
  annotations:
    k8s.aliyun.com/eci-gpu-type : "P4"
spec:
  containers:
  - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
    name: tensorflow
    command:
    - "sh"
    - "-c"
    - "python models/tutorials/image/imagenet/classify_image.py"
    resources:
      limits:
        nvidia.com/gpu: "1"
  restartPolicy: OnFailure
  nodeName: virtual-kubelet
```

**说明：** 基于虚拟节点的方式可以更灵活的支持多种深度学习框架，例如Kubeflow、Arena或其他自定义CRD。

