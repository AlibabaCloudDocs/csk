# 镜像创建有状态StatefulSet应用 {#task_o24_h1s_2fb .task}

阿里云容器服务Kubernetes集群支持通过界面创建StatefulSet类型的应用，满足您快速创建有状态应用的需求。本例中将创建一个nginx的有状态应用，并演示StatefulSet应用的特性。

-   您已成功创建一个 Kubernetes 集群。参见[ZH-CN\_TP\_16639.md\#](intl.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes 集群.md#)。
-   您已成功创建一个云盘存储卷声明，参见[ZH-CN\_TP\_16691.md\#](intl.zh-CN/Kubernetes集群用户指南/存储管理-Flexvolume/创建持久化存储卷声明.md#)。
-   您已连接到Kubernetes集群的Master节点，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。

StatefulSet包括如下特性：

|场景|说明|
|--|--|
|Pod一致性|包含次序（启动、停止次序）、网络一致性。此一致性与Pod相关，与被调度到哪个node节点无关。|
|稳定的持久化存储|通过VolumeClaimTemplate为每个Pod创建一个PV。删除、减少副本，不会删除相关的卷。|
|稳定的网络标志|Pod的hostname模式为：`(statefulset名称)−(序号)`。|
|稳定的次序|对于N个副本的StatefulSet，每个Pod都在\[0，N）的范围内分配一个数字序号，且是唯一的。|

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在Kubernetes菜单下，单击左侧导航栏中的**应用** \> **有状态**，然后单击页面右上角的**使用镜像创建**。
3.  在应用基本信息页面进行设置，然后单击**下一步** 进入应用配置页面。 

    -   **应用名称**：设置应用的名称。
    -   **部署集群**：设置应用部署的集群。
    -   **命名空间**：设置应用部署所处的命名空间，默认使用default命名空间。
    -   **副本数量**：即应用包含的Pod数量。
    -   **类型**：可选择无状态（Deployment）和有状态（StatefulSet）两种类型。

        **说明：** 本例中选择**有状态**类型，创建StatefulSet类型的应用。

    -   **标签**：为该应用添加一个标签，标识该应用。
    -   **注解**：为该应用添加一个注解（annotation）。
    ![应用配置页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927312304_zh-CN.png)

4.  设置容器配置。 

    **说明：** 您可为应用的Pod设置多个容器。

    1.  设置容器的基本配置。 

        -   **镜像名称**：您可以单击**选择镜像**，在弹出的对话框中选择所需的镜像并单击**确定**，本例中为 nginx。

            您还可以填写私有 registry。填写的格式为`domainname/namespace/imagename:tag`

        -   **镜像版本**：您可以单击**选择镜像版本** 选择镜像的版本。若不指定，默认为 latest。
        -   **总是拉取镜像**：为了提高效率，容器服务会对镜像进行缓存。部署时，如果发现镜像 Tag 与本地缓存的一致，则会直接复用而不重新拉取。所以，如果您基于上层业务便利性等因素考虑，在做代码和镜像变更时没有同步修改 Tag ，就会导致部署时还是使用本地缓存内旧版本镜像。而勾选该选项后，会忽略缓存，每次部署时重新拉取镜像，确保使用的始终是最新的镜像和代码。
        -   **镜像密钥**：单击**设置镜像密钥**设置镜像的密钥。对于私有仓库访问时，需要设置密钥，具体可以参见[使用镜像密钥](intl.zh-CN/Kubernetes集群用户指南/应用管理/使用镜像密钥.md#)。
        -   **资源限制**：可指定该应用所能使用的资源上限，包括 CPU 和 内存两种资源，防止占用过多资源。其中，CPU 资源的单位为 millicores，即一个核的千分之一；内存的单位为 Bytes，可以为 Gi、Mi 或 Ki。
        -   **所需资源**：即为该应用预留资源额度，包括 CPU 和 内存两种资源，即容器独占该资源，防止因资源不足而被其他服务或进程争夺资源，导致应用不可用。
        -   **Init Container**：勾选该项，表示创建一个Init Container，Init Container包含一些实用的工具，具体参见[https://kubernetes.io/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)。
        ![设置容器基本信息](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927312305_zh-CN.png)

    2.  配置**环境变量**。 

        支持通过键值对的形式为 Pod 配置环境变量。用于给 Pod 添加环境标志或传递配置等，具体请参见 [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE)。

    3.  配置**健康检查**。 支持存活检查（liveness）和就绪检查（Readiness）。存活检查用于检测何时重启容器；就绪检查确定容器是否已经就绪，且可以接受流量。关于健康检查的更多信息，请参见[https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927310977_zh-CN.png)

        |请求类型|配置说明|
        |----|----|
        |HTTP请求|即向容器发送一个HTTPget 请求，支持的参数包括：         -   协议：HTTP/HTTPS
        -   路径：访问HTTP server 的路径
        -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
        -   HTTP头：即HTTPHeaders，HTTP请求中自定义的请求头，HTTP允许重复的header。支持键值对的配置方式。
        -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为3秒。
        -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10s，最小为1s。
        -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
 |
        |TCP连接|即向容器发送一个TCP Socket，kubelet将尝试在指定端口上打开容器的套接字。 如果可以建立连接，容器被认为是健康的，如果不能就认为是失败的。支持的参数包括：         -   端口：容器暴露的访问端口或端口名，端口号必须介于1~65535。
        -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为15秒。
        -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10s，最小为1s。
        -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
 |
        |命令行|通过在容器中执行探针检测命令，来检测容器的健康情况。支持的参数包括：         -   命令行：用于检测容器健康情况的探测命令。
        -   延迟探测时间（秒）：即initialDelaySeconds，容器启动后第一次执行探测时需要等待多少秒，默认为5秒。
        -   执行探测频率（秒）：即periodSeconds，指执行探测的时间间隔，默认为10s，最小为1s。
        -   超时时间（秒）：即timeoutSeconds，探测超时时间。默认1秒，最小1秒。
        -   健康阈值：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1，最小值是1。对于存活检查（liveness）必须是1。
        -   不健康阈值：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
 |

    4.  配置生命周期。 

        您可以为容器的生命周期配置启动执行、启动后处理和停止前处理。具体参见[https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/)。

        -   **启动执行**：为容器设置预启动命令和参数。
        -   **启动后处理**：为容器设置启动后的命令。
        -   **停止前处理**：为容器设置预结束命令。
        ![配置生命周期](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927311371_zh-CN.png)

    5.  配置数据卷信息。 

        支持配置本地存储和云存储。

        -   **本地存储**：支持主机目录（hostpath）、配置项（configmap）、保密字典（secret）和临时目录，将对应的挂载源挂载到容器路径中。更多信息参见 [volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE)。
        -   **云存储**：支持云盘/NAS/OSS三种云存储类型。
        本例中配置了一个云盘类型的数据卷声明disk-ssd，将其挂载到容器的/data 路径下。

        ![配置数据卷](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927312307_zh-CN.png)

    6.  配置**日志服务**，您可进行采集配置和自定义Tag设置。 

        **说明：** 请确保已部署Kubernetes集群，并且在此集群上已安装日志插件。

        您可对日志进行采集配置：

        -   **日志库**：即在日志服务中生成一个对应的logstore，用于存储采集到的日志。
        -   **容器内日志路径**：支持stdout和文本日志。
            -   **stdout**： stdout 表示采集容器的标准输出日志。
            -   **文本日志**：表示收集容器内指定路径的日志，本例中表示收集/var/log/nginx下所有的文本日志，也支持通配符的方式。
        您还可设置自定义 tag，设置tag后，会将该tag一起采集到容器的日志输出中。自定义 tag 可帮助您给容器日志打上tag，方便进行日志统计和过滤等分析操作。

        ![配置日志采集](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927311364_zh-CN.png)

5.  完成容器配置后，单击 **下一步**。
6.  进行高级设置。本例中仅进行访问设置。 
    1.  设置**访问设置**。 您可以设置暴露后端Pod的方式，最后单击**创建**。本例中选择ClusterIP服务和路由（Ingress），构建一个公网可访问的nginx应用。

        **说明：** 

        针对应用的通信需求，您可灵活进行访问设置：

        -   内部应用：对于只在集群内部工作的应用，您可根据需要创建ClusterIP或NodePort类型的服务，来进行内部通信。
        -   外部应用：对于需要暴露到公网的应用，您可以采用两种方式进行访问设置：
            -   创建LoadBalancer类型的服务：使用阿里云提供的负载均衡服务（Server Load Balancer，SLB），该服务提供公网访问能力。
            -   创建ClusterIP、NodePort类型的服务，以及路由（Ingress）：通过路由提供公网访问能力，详情参见[https://kubernetes.io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/)。
        ![访问设置](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927310979_zh-CN.png)

        1.  在服务栏单击**创建**，在弹出的对话框中进行配置，最后单击**创建**。

            ![创建服务](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927412327_zh-CN.png)

            -   **名称**：您可自主设置，默认为`applicationname-svc`。
            -   **类型**：您可以从下面 3 种服务类型中进行选择。
                -   虚拟集群 IP：即 ClusterIP，指通过集群的内部 IP 暴露服务，选择该项，服务只能够在集群内部可以访问。
                -   节点端口：即 NodePort，通过每个 Node 上的 IP 和静态端口（NodePort）暴露服务。NodePort 服务会路由到 ClusterIP 服务，这个 ClusterIP 服务会自动创建。通过请求 `<NodeIP>:<NodePort>`，可以从集群的外部访问一个 NodePort 服务。
                -   负载均衡：即 LoadBalancer，是阿里云提供的负载均衡服务，可选择公网访问或内网访问。负载均衡可以路由到 NodePort 服务和 ClusterIP 服务。
            -   **端口映射**：您需要添加服务端口和容器端口，若**类型**选择为节点端口，还需要自己设置节点端口，防止端口出现冲突。支持 TCP/UDP 协议。
            -   **注解**：为该服务添加一个注解（annotation），支持负载均衡配置参数，参见[ZH-CN\_TP\_16677.md\#](intl.zh-CN/Kubernetes集群用户指南/网络管理/通过负载均衡（Server Load Balancer）访问服务.md#)。
            -   **标签**：您可为该服务添加一个标签，标识该服务。
        2.  在路由栏单击**创建**，在弹出的对话框中，为后端Pod配置路由规则，最后单击**创建**。更多详细的路由配置信息，请参见[ZH-CN\_TP\_16681.md\#](intl.zh-CN/Kubernetes集群用户指南/网络管理/路由配置说明.md#)。

            **说明：** 通过镜像创建应用时，您仅能为一个服务创建路由（Ingress）。本例中使用一个虚拟主机名称作为测试域名，您需要在hosts中添加一条记录。在实际工作场景中，请使用备案域名。

            ``` {#codeblock_5dk_u1n_5n5}
            101.37.224.146   foo.bar.com    #即ingress的IP
            ```

            ![创建路由](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927410981_zh-CN.png)

        3.  在访问设置栏中，您可看到创建完毕的服务和路由，您可单击**变更**和**删除**进行二次配置。

            ![变更或删除路由](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927410982_zh-CN.png)

    2.  **容器组水平伸缩**。 

        您可勾选是否开启**容器组水平伸缩**，为了满足应用在不同负载下的需求，容器服务支持服容器组（Pod）的弹性伸缩，即根据容器 CPU 和内存资源占用情况自动调整容器组数量。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927410978_zh-CN.png)

        **说明：** 若要启用自动伸缩，您必须为容器设置所需资源，否则容器自动伸缩无法生效。参见容器基本配置环节。

        -   **指标**：支持CPU和内存，需要和设置的所需资源类型相同。
        -   **触发条件**：资源使用率的百分比，超过该使用量，容器开始扩容。
        -   **最大副本数量**：该StatefulSet可扩容的容器数量上限。
        -   **最小副本数量**：该StatefulSet可缩容的容器数量下限。
    3.  设置**调度设置**。 

        您可设置升级方式、节点亲和性、应用亲和性和应用非亲和性，详情参见[https://kubernetes.io/docs/concepts/configuration/assign-pod-node/\#affinity-and-anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity)。

        **说明：** 亲和性调度依赖节点标签和Pod标签，您可使用内置的标签进行调度；也可预先为节点、Pod配置相关的标签。

        1.  设置**升级方式**。

            升级方式包括滚动升级（rollingupdate）和替换升级（recreate），详细请参见[https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/)

        2.  设置**节点亲和性**，通过Node节点的Label标签进行设置。

            ![节点亲和性](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927411137_zh-CN.png)

            节点调度支持硬约束和软约束（Required/Preferred），以及丰富的匹配表达式（In, NotIn, Exists, DoesNotExist. Gt, and Lt）：

            -   **必须满足**，即硬约束，一定要满足，对应requiredDuringSchedulingIgnoredDuringExecution，效果与`NodeSelector`相同。本例中Pod只能调度到具有对应标签的Node节点。您可以定义多条硬约束规则，但只需满足其中一条。
            -   **尽量满足**，即软约束，不一定满足，对应preferredDuringSchedulingIgnoredDuringExecution。本例中，调度会尽量不调度Pod到具有对应标签的Node节点。您还可为软约束规则设定权重，具体调度时，若存在多个符合条件的节点，权重最大的节点会被优先调度。您可定义多条软约束规则，但必须满足全部约束，才会进行调度。
        3.  设置**应用亲和性调度**。决定应用的Pod可以和哪些Pod部署在同一拓扑域。例如，对于相互通信的服务，可通过应用亲和性调度，将其部署到同一拓扑域（如同一个主机）中，减少它们之间的网络延迟。

            ![应用亲和性调度](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156897927411222_zh-CN.png)

            根据节点上运行的Pod的标签（Label）来进行调度，支持硬约束和软约束，匹配的表达式有：`In, NotIn, Exists, DoesNotExist`。

            -   **必须满足**，即硬约束，一定要满足，对应requiredDuringSchedulingIgnoredDuringExecution，Pod的亲和性调度必须要满足后续定义的约束条件。
                -   **命名空间**：该策略是依据Pod的Label进行调度，所以会受到命名空间的约束。
                -   **拓扑域**：即topologyKey，指定调度时作用域，这是通过Node节点的标签来实现的，例如指定为`kubernetes.io/hostname`，那就是以Node节点为区分范围；如果指定为`beta.kubernetes.io/os`，则以Node节点的操作系统类型来区分。
                -   **选择器**：单击选择器右侧的加号按钮，您可添加多条硬约束规则。
                -   **查看应用列表**：单击**应用列表**，弹出对话框，您可在此查看各命名空间下的应用，并可将应用的标签导入到亲和性配置页面。
                -   硬约束条件：设置已有应用的标签、操作符和标签值。本例中，表示将待创建的应用调度到该主机上，该主机运行的已有应用具有`app:nginx`标签。
            -   **尽量满足**，即软约束，不一定满足，对应preferredDuringSchedulingIgnoredDuringExecution。Pod的亲和性调度会尽量满足后续定义的约束条件。对于软约束规则，您可配置每条规则的权重，其他配置规则与硬约束规则相同。

                **说明：** **权重**：设置一条软约束规则的权重，介于1-100，通过算法计算满足软约束规则的节点的权重，将Pod调度到权重最大的节点上。

        4.  设置**应用非亲和性调度**，决定应用的Pod不与哪些Pod部署在同一拓扑域。应用非亲和性调度的场景包括：

            -   将一个服务的Pod分散部署到不同的拓扑域（如不同主机）中，提高服务本身的稳定性。
            -   给予Pod一个节点的独占访问权限来保证资源隔离，保证不会有其它Pod来分享节点资源。
            -   把可能会相互影响的服务的Pod分散在不同的主机上。
            **说明：** 应用非亲和性调度的设置方式与亲和性调度相同，但是相同的调度规则代表的意思不同，请根据使用场景进行选择。

7.  最后单击**创建**。
8.  创建成功后，默认进入创建完成页面，会列出应用包含的对象，您可以单击**查看应用详情**进行查看。 

    ![查看详情1](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927412341_zh-CN.png)

    默认进入有状态副本集详情页面。

    ![查看副本详情](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927412343_zh-CN.png)

9.  然后单击左上角**返回列表**，进入有状态副本集列表页面，查看创建的StatefulSet应用。 

    ![查看应用](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927412431_zh-CN.png)

10. 选择所需的nginx应用，单击右侧**伸缩**，验证服务伸缩性。 
    1.  在弹出的对话框中，将容器组数量设置为3，您可发现扩容时，扩容容器组的排序依次递增；反之，进行缩容时，先按Pod次序从高到低进行缩容。这体现StatefulSet中Pod的次序稳定性。 

        ![验证服务伸缩](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927412438_zh-CN.png)

    2.  单击左侧导航栏中的**应用** \> **存储声明**，您可发现，随着应用扩容，会随着Pod创建新的云盘卷；缩容后，已创建的PV/PVC不会删除。 

        ![存储声明](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21663/156897927412450_zh-CN.png)


连接到Master节点，执行以下命令，验证持久化存储特性。

在云盘中创建临时文件：

``` {#codeblock_knd_08m_md4}
# kubectl exec nginx-1 ls /tmp            #列出该目录下的文件
lost+found

# kubectl exec nginx-1 touch /tmp/statefulset         #增加一个临时文件statefulset

# kubectl exec nginx-1 ls /tmp
lost+found
statefulset
```

删除Pod，验证数据持久性：

``` {#codeblock_zri_z7c_fsu}
# kubectl delete pod nginx-1
pod"nginx-1" deleted

# kubectl exec nginx-1 ls /tmp                         #数据持久化存储
lost+found
statefulset
```

此外，您还可发现，删除容器组后，过一段时间，容器组（Pod）会自动重启，证明StatefulSet应用的高可用性。

