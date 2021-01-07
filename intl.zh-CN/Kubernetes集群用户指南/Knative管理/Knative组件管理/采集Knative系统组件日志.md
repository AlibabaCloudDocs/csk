---
keyword: [Knative系统组件, 采集日志, Docker标准输出]
---

# 采集Knative系统组件日志

您可以采集Knative系统组件的日志，这样便于后续通过日志进行组件运行分析及问题排查。本文介绍如何通过日志服务对Knative系统组件进行Docker标准输出日志采集。

[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)

Knative系统组件包括：

-   knative-serving
    -   activator
    -   autoscaler
    -   autoscaler-hpa
    -   controller
    -   istio-webhook
    -   networking-istio
    -   webhook
-   knative-eventing
    -   eventing-controller
    -   eventing-webhook

## 操作步骤

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏单击**集群信息**，单击**集群资源**页签。

5.  在**集群资源**页签单击**日志服务Project**右侧的链接。

6.  在日志服务项目概览页面右上角，单击**接入数据**。

7.  在接入数据对话框，单击**Docker标准输出-容器**。

8.  完成Docker标准输出数据接入配置。

    具体步骤，请参见[创建采集配置](/intl.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-控制台方式采集Kubernetes标准输出.md)。

    本文以采集knative-serving中的controller组件为例，说明如何配置**数据源设置**。示例代码如下。

    ```
    {
        "inputs": [
            {
                "detail": {
                    "IncludeEnv": {
              "SYSTEM_NAMESPACE":"knative-serving"
            },
                    "IncludeLabel": {
              "io.kubernetes.container.name": "controller"
                    },
                    "ExcludeLabel": {}
                },
                "type": "service_docker_stdout"
            }
        ]
    }
    ```

    ![数据采集](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4895659951/p127942.png)

    **说明：**

    -   IncludeEnv中SYSTEM\_NAMESPACE设置对应的命名空间。
    -   IncludeLabel中io.kubernetes.container.name设置相应组件的名称。
9.  在**查询日志**区域，单击**立即尝试**查看采集结果。

    ![日志查询](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4895659951/p127947.png)


