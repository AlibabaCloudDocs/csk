# 创建标准托管版安全沙箱Kubernetes集群

调用CreateCluster创建一个标准托管版安全沙箱Kubernetes集群。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=CreateCluster&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见[公共请求头部](/cn.zh-CN/API参考/公共请求和返回结果.md)。

## 请求语法

```
POST /clusters
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
| |Object|Body|是| |创建标准托管版安全沙箱Kubernetes集群。|
|addons|Array|Body|是|\[\{"name": "terway-eniip","config": ""\}, \{"name": "logtail-ds","config": "\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}, \{"name":"nginx-ingress-controller","config":"\{\\"IngressSlbNetworkType\\":\\"internet\\"\}"\}\]|Kubernetes集群的安装的组件列表：-   组件的参数：
    -   `name`：必选。
    -   `config`：可选，取值为空时表示无需配置。
    -   `disabled`：可选，是否禁止默认安装。
-   网络插件：必选，包含Flannel和Terway网络两种网络类型，创建集群时二选一。
    -   Flannel网络：`[{"name":"flannel","config":""}]`。
    -   Terway网络：`[{"name": "terway-eniip","config": "\"IPVlan\":\"false\""}]`。
-   存储插件：必选，支持CSI和FlexVolume两种类型。
    -   `csi`插件：`[{"name":"csi-plugin","config": ""},{"name": "csi-provisioner","config": ""}]`。
    -   `flexvolume`插件：`[{"name": "flexvolume","config": ""}]`。
-   日志组件：可选。

**说明：** 如果不开启日志服务时，将无法使用集群审计功能。

    -   使用已有SLS Project：`[{"name": "logtail-ds","config": "{"IngressDashboardEnabled":"true","sls_project_name":"your_sls_project_name"}"}]`。
    -   创建新的SLS Project：`[{"name": "logtail-ds","config": "{"IngressDashboardEnabled":"true"}"}]`。
-   Ingress：可选，默认开启安装Ingress组件nginx-ingress-controller：
    -   安装Ingress并且开启公网：`[{"name":"nginx-ingress-controller","config":"{"IngressSlbNetworkType":"internet"}"}]`。
    -   不安装Ingress：`[{"name": "nginx-ingress-controller","config": "","disabled": true}]`。
-   事件中心：可选，默认开启。事件中心提供对Kubernetes事件的存储、查询、告警等能力。Kubernetes事件中心关联的Logstore在90天内免费。关于免费策略的更多信息，请参见[创建并使用Kubernetes事件中心](/cn.zh-CN/应用中心（App）/K8S事件中心/创建并使用Kubernetes事件中心.md)。

开启事件中心：`[{"name":"ack-node-problem-detector","config":"{\"sls_project_name\":\" your_sls_project_name\"}"}]`。 |
|cloud\_monitor\_flags|Boolean|Body|否|true|集群是否安装云监控插件。取值：-   `true`：集群不安装云监控插件。
-   `false`：集群安装云监控插件。

默认值：`false`。 |
|cluster\_type|String|Body|是|ManagedKubernetes|集群类型。创建标准托管版安全沙箱集群时，只能选`ManagedKubernetes`。|
|container\_cidr|String|Body|否|172.20.0.0/16|Pod网络地址段，不能和VPC网段冲突。当选择系统自动创建VPC时，默认使用172.16.0.0/16网段。当创建Flannel网络类型的集群时，该字段为必选。|
|cpu\_policy|String|Body|否|none|节点CPU管理策略。当集群版本在1.12.6及以上时支持以下两种策略：-   `static`：允许为节点上具有某些资源特征的Pod赋予增强的CPU亲和性和独占性。
-   `none`：表示启用现有的默认CPU亲和方案。

默认值：`none`。 |
|deletion\_protection|Boolean|Body|否|true|集群是否开启集群删除保护，防止通过控制台或API误删除集群。取值：-   `true`：集群开启集群删除保护。
-   `false`：集群不开启集群删除保护。

默认值：`false`。 |
|disable\_rollback|Boolean|Body|否|true|集群创建失败是否回滚。取值：-   `true`：集群创建失败回滚。
-   `false`：集群创建失败不回滚。

默认值：`false`。 |
|endpoint\_public\_access|Boolean|Body|否|true|是否开启公网API Server。取值：-   `true`：表示开放公网API Server。
-   `false`：表示不会创建公网的API Server，仅创建私网的API Server。

默认值：`true`。 |
|is\_enterprise\_security\_group|Boolean|Body|否|true|自动创建企业级安全组，当security\_group\_id为空时生效。Terway网络类型集群，必须指定为企业安全组。-   `true`：自动创建企业级安全组。
-   `false`：不自动创建企业级安全组。

默认值：`false`。|
|key\_pair|String|Body|是|secrity-key|密钥对名称，和`login_password`二选一。|
|kubernetes\_version|String|Body|否|1.16.9-aliyun.1|集群版本，与Kubernetes社区基线版本保持一致。建议选择最新版本，若不指定，默认使用最新版本。目前您可以在ACK控制台创建两种最新版本的集群。您可以通过API创建其他Kubernetes版本集群。关于ACK支持的Kubernetes版本，请参见[Kubernetes版本发布概览](/cn.zh-CN/新功能发布记录/Kubernetes版本发布说明/Kubernetes版本发布概览.md)。|
|login\_password|String|Body|是|Hello@1234|SSH登录密码，和`key_pair`二选一。密码规则为8~30个字符，且至少同时包含三项（大小写字母、数字和特殊符号）。|
|name|String|Body|是|cluster-demo|集群名称。命名规则：由数字、汉字、英文字符或短划线（-）组成，长度范围1~63个字符，且不能以短划线（-）开头。|
|node\_cidr\_mask|String|Body|否|25|节点IP数量，通过指定网络的CIDR来确定IP的数量，只对于Flannel网络类型集群生效。默认值：25。 |
|node\_port\_range|String|Body|否|30000~32767|节点服务端口范围。取值范围为\[30000,65535\]。|
|num\_of\_nodes|Long|Body|是|3|Worker节点数。范围是\[0,100\]。|
|pod\_vswitch\_ids|Array of String|Body|否|vsw-2ze97jwri7cei0mpw\*\*\*\*|Pod的虚拟交换机列表。**说明：** Terway网络类型必须要指定`pod_vswitch_ids`。

您需要为每一个节点虚拟交换机指定至少一个相同可用区的Pod虚拟交换机，并且不能跟节点`vswitch`重复。|
|proxy\_mode|String|Body|否|ipvs|kube-proxy代理模式，支持`iptables`和`ipvs`两种模式。默认为`ipvs`。 |
|region\_id|String|Body|是|cn-beijing|集群所在的地域ID。|
|runtime|Object|Body|是| |集群内容器运行时。**说明：** 此参数为必选参数。 |
|name|Object|Body|是|Sandboxed-Container.runv|容器运行时版本。容器运行时名称。取值：-   `Sandboxed-Container.runv`：安全沙箱运行时。
-   `docker`：Docker运行时。

默认：`docker`。

**说明：** 在创建安全沙箱集群的场景下，只能选`Sandboxed-Container.runv`。 |
|version|Object|Body|是|2.1.0|容器运行时版本，默认为最新版。关于安全沙箱运行时的变更详情，请参见[安全沙箱运行时变更记录](/cn.zh-CN/新功能发布记录/组件介绍与变更记录/安全沙箱/安全沙箱运行时变更记录.md)。|
|security\_group\_id|String|Body|否|sg-bp1bdue0qc1g7k\*\*\*\*|使用已有安全组创建集群时需要指定安全组ID，集群节点自动加入到此安全组，和`is_enterprise_security_group`二选一。|
|service\_cidr|String|Body|是|172.21.0.0/20|Service网络地址段，不能和VPC网段及Pod网络网段冲突。当选择系统自动创建VPC时，默认使用172.19.0.0/20网段。|
|snat\_entry|Boolean|Body|否|true|是否为网络配置SNAT：-   当已有VPC能访问公网环境时，设置为`false`。
-   当已有VPC不能访问公网环境时：
    -   设置为`true`，表示配置SNAT，此时可以访问公网环境。
    -   设置为`false`，表示不配置SNAT，此时不能访问公网环境。

如果您的应用需要访问公网，建议配置为`true`。

默认值：`false`。 |
|ssh\_flags|Boolean|Body|否|true|是否开放公网SSH登录。取值：-   `true`：表示开放。
-   `false`：表示不开放。

默认值：`false`。|
|tags|Array|Body|否| |集群标签。|
|key|String|Body|否|env|标签key-value的键。|
|value|String|Body|否|prod|标签key-value的值。|
|taints|Array|Body|否| |节点污点信息。污点和容忍度（Toleration）相互配合，可以用来避免Pod被分配到不合适的节点上。更多信息，请参见[taint-and-toleration](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/)|
|effect|String|Body|否|NoSchedule|调度策略。取值：-   NoSchedule：Pod不会被调度到标记了Taints的节点上。
-   NoExecute：一旦Taint生效，该节点内正在运行的Pod没有对应Tolerate设置，会直接被逐出。
-   PreferNoSchedule：NoSchedule的宽松版本。避免将Pod调度到有污点的节点上。 |
|key|String|Body|否|disk\_type|污点key-value的键。|
|value|String|Body|否|sshd|污点key-value的值。|
|timeout\_mins|Long|Body|否|60|集群创建超时时间。默认值：60。 |
|user\_data|String|Body|否|IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFD\*\*\*\*|节点自定义数据。更多信息 ，请参见[生成实例自定义数据](/cn.zh-CN/实例/管理实例/使用实例自定义数据/生成实例自定义数据.md)。|
|vpcid|String|Body|是|vpc-2zeik9h3ahvv2zz95\*\*\*\*|集群使用的VPC实例ID。|
|vswitch\_ids|Array of String|Body|是|vsw-2ze48rkq464rsdts1\*\*\*\*"|虚拟交换实例ID。|
|worker\_auto\_renew|Boolean|Body|否|true|Worker节点是否开启自动续费，当`worker_instance_charge_type`取值为`PrePaid`时才生效，可选值为：-   true：自动续费。
-   false：不自动续费。

默认值：true。|
|worker\_auto\_renew\_period|Long|Body|否|1|Worker节点自动续费周期，当选择包年包月付费类型时才生效，且为必选值。取值范围：\{1,2,3,6,12\}。|
|worker\_data\_disks|Array|Body|是| |Worker节点数据盘类型、大小等配置的组合。|
|auto\_snapshot\_policy\_id|String|String|否|sp-bp14j6w7ss6ozz\*\*\*\*|自动快照策略ID。|
|category|String|String|否|cloud\_ssd|Worker节点数据盘类型，取值：-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。
-   `cloud`：普通云盘。

默认值：`cloud_efficiency`。|
|encrypted|String|String|否|false|是否对数据盘加密。取值：-   `true`：对数据盘加密。
-   `false`：不对数据盘加密。

默认值：`false`。 |
|size|String|String|是|200|数据盘大小，内存单位为GiB。取值范围：-   cloud\_efficiency：\[20,32768\]
-   cloud\_ssd：\[20,32768\]
-   cloud：\[5,2000\]

**说明：** 安全沙箱节点要求至少有一块不小于200 GiB的数据盘。 |
|worker\_instance\_charge\_type|String|Body|是|PrePaid|Worker节点付费类型，可选值为：-   `PrePaid`：包年包月。
-   `PostPaid`：按量付费。

默认：按量付费。|
|worker\_instance\_types|Array of String|Body|是|ecs.ebmg5s.24xlarge|Worker节点实例类型。**说明：** 安全沙箱集群只支持**神龙裸金属实例**。 |
|worker\_period|Long|Body|否|1|Worker节点包年包月时长，当`worker_instance_charge_type`取值为`PrePaid`时才生效且为必选值。取值范围：\{1,2,3,6,12,24,36,48,60\}。默认值：1。 |
|worker\_period\_unit|String|Body|否|Month|Wroker节点付费周期，当付费类型为`PrePaid`的时候需要指定周期。取值：`Month`，当前只支持以月为周期。|
|worker\_system\_disk\_category|String|Body|否|cloud\_efficiency|Worker节点系统盘类型，取值：-   `cloud_efficiency`：高效云盘。
-   `cloud_ssd`：SSD云盘。

默认值：`cloud_ssd`。 |
|worker\_system\_disk\_size|Long|Body|否|120|Worker节点系统盘大小，单位为GiB。取值范围：\[20,500\]。该参数的取值必须大于或者等于max\{20, ImageSize\}。

默认值：max\{40, 参数ImageId对应的镜像大小\}。 |
|worker\_vswitch\_ids|Array of String|Body|否|vsw-2ze3ds0mdip0hdz8i\*\*\*\*|集群节点使用的虚拟交换机列表，一个节点对应一个值。|
|zone\_id|String|Body|否|cn-beijing-b|集群所属地域的可用区ID。|

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|cluster\_id|String|cb95aa626a47740afbf6aa099b650\*\*\*\*|集群ID。|
|request\_id|String|687C5BAA-D103-4993-884B-C35E4314A1E1|请求ID。|
|task\_id|String|T-5a54309c80282e39ea00002f|任务ID。|

## 示例

请求示例

```
POST /clusters
公共请求头
{
  "name": "webService",
  "cluster_type": "ManagedKubernetes",
  "disable_rollback": true,
  "timeout_mins": 60,
  "kubernetes_version": "1.18.8-aliyun.1",
  "region_id": "cn-hangzhou",
  "snat_entry": true,
  "cloud_monitor_flags": true,
  "endpoint_public_access": false,
  "deletion_protection": false,
  "node_cidr_mask": "26",
  "proxy_mode": "ipvs",
  "tags": [],
  "timezone": "Asia/Shanghai",
  "addons": [{
    "name": "flannel"
  }, {
    "name": "sandboxed-container-controller"
  }, {
    "name": "csi-plugin"
  }, {
    "name": "csi-provisioner"
  }, {
    "name": "logtail-ds",
    "config": "{\"IngressDashboardEnabled\":\"true\"}"
  }, {
    "name": "ack-node-problem-detector",
    "config": "{\"sls_project_name\":\"\"}"
  }, {
    "name": "nginx-ingress-controller",
    "config": "{\"IngressSlbNetworkType\":\"internet\"}"
  }, {
    "name": "arms-prometheus"
  }],
  "runtime": {
    "name": "Sandboxed-Container.runv",
    "version": "2.1.0"
  },
  "worker_instance_types": ["ecs.ebmc5s.24xlarge"],
  "num_of_nodes": 3,
  "worker_system_disk_category": "cloud_essd",
  "worker_system_disk_size": 120,
  "worker_data_disks": [{
    "category": "cloud_efficiency",
    "size": "200",
    "encrypted": "false",
    "auto_snapshot_policy_id": ""
  }],
  "worker_instance_charge_type": "PostPaid",
  "vpcid": "vpc-bp1gxh70jnkl12vq27jg7",
  "container_cidr": "172.23.0.0/16",
  "service_cidr": "172.21.0.0/20",
  "vswitch_ids": ["vsw-bp1hl2o4i9z7sbmy*****"],
  "login_password": "Hello1234!",
  "logging_type": "SLS",
  "cpu_policy": "none",
  "is_enterprise_security_group": true
}
```

请求示例补充说明

```
如果是terway网络类型的集群，pod_vswitch_ids为必选参数，请求入参示例如下：
POST /clusters HTTP/1.1
公共请求头
{
  "name": "webService",
  "cluster_type": "ManagedKubernetes",
  "disable_rollback": true,
  "timeout_mins": 60,
  "kubernetes_version": "1.18.8-aliyun.1",
  "region_id": "cn-hangzhou",
  "snat_entry": true,
  "cloud_monitor_flags": true,
  "endpoint_public_access": false,
  "deletion_protection": false,
  "proxy_mode": "ipvs",
  "tags": [],
  "timezone": "Asia/Shanghai",
  "addons": [{
    "name": "terway-eniip",
    "config": "{\"IPVlan\":\"false\",\"NetworkPolicy\":\"false\"}"
  }, {
    "name": "sandboxed-container-controller"
  }, {
    "name": "csi-plugin"
  }, {
    "name": "csi-provisioner"
  }, {
    "name": "logtail-ds",
    "config": "{\"IngressDashboardEnabled\":\"true\"}"
  }, {
    "name": "ack-node-problem-detector",
    "config": "{\"sls_project_name\":\"\"}"
  }, {
    "name": "nginx-ingress-controller",
    "config": "{\"IngressSlbNetworkType\":\"internet\"}"
  }, {
    "name": "arms-prometheus"
  }],
  "pod_vswitch_ids": ["vsw-bp1e5819t8dl8ulcrpgkm"],
  "runtime": {
    "name": "Sandboxed-Container.runv",
    "version": "2.1.0"
  },
  "worker_instance_types": ["ecs.ebmc5s.24xlarge"],
  "num_of_nodes": 3,
  "worker_system_disk_category": "cloud_essd",
  "worker_system_disk_size": 120,
  "worker_data_disks": [{
    "category": "cloud_efficiency",
    "size": "200",
    "encrypted": "false",
    "auto_snapshot_policy_id": ""
  }],
  "worker_instance_charge_type": "PostPaid",
  "vpcid": "vpc-bp1gxh70jnkl12vq27jg7",
  "service_cidr": "172.21.0.0/20",
  "vswitch_ids": ["vsw-bp1hl2o4i9z7sbmy*****"],
  "login_password": "Hello1234!",
  "logging_type": "SLS",
  "cpu_policy": "none",
  "is_enterprise_security_group": true
}
```

正常返回示例

`XML`格式

```
<cluster_id>cb95aa626a47740afbf6aa099b650****</cluster_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
```

`JSON`格式

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "task_id": "T-5a54309c80282e39ea00002f",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1"
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。

