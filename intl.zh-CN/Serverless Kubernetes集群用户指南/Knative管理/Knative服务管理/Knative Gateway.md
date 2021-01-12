# Knative Gateway

Kative Gateway具备多种Gateway实现方案，并且属于云产品级别的支撑，不需要常驻资源。不仅可以帮助您节省laaS成本还省去了很多运维负担。

## Knative Gateway优势

社区Knative默认支持Istio、Gloo、Contour、Kourier和Ambassador等多种Gateway实现方案。在这些实现方案中Istio使用频率最高。因为Istio除了可以充当Gateway的角色还能作为ServiceMesh服务使用。Serverless服务首先需要有Gateway实例常驻运行，而且为了保证高可用至少要有两个实例互为备份。其次这些Gateway的k8s Controller也需要常驻运行，这些常驻实例的IaaS费用和运维都是业务需要支付的成本。

ASK Knative使用SLB作为Gateway。除了HTTP以外，Gateway还提供HTTPS功能。Knative默认给Gateway生成一个自签名的HTTPS证书，没有域名限制，可用于测试。在使用Knative部署线上服务时，您需要创建HTTPS证书，并将Knative Gateway Service的证书ID修改为创建的证书ID，更多信息，请参见[配置HTTPS证书](/intl.zh-CN/Serverless Kubernetes集群用户指南/Knative管理/配置HTTPS证书.md)。

为了极致的Serverless体验，通过阿里云SLB实现了Knative Gateway。Knative Gateway具备所有需要的功能并且属于云产品级别的支撑，不需要常驻资源。不仅节省了您的IaaS成本还省去了很多运维负担。

![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3903222061/p171606.png)

## 使用Knative Gateway

1.  获取SLB的IP地址。

    输入以下命令：

    ```
    kubectl -n knative-serving get svc
    ```

    系统输出类似如下结果：

    ```
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
    ingress-gateway   LoadBalancer   172.19.1*.***   106.15.2**.**   80:32185/TCP,443:31137/TCP   69d
    ```

2.  访问服务。

    -   通过HTTP访问服务。

        输入以下命令：

        ```
        curl -H  "Host: helloworld-go.default.example.com" http://106.15.2**.**
        ```

        系统输出类似如下结果：

        ```
        Hello Knative!
        ```

    -   通过HTTPS访问服务。

        输入以下命令：

        ```
        curl -H  "Host: helloworld-go.default.example.com" https://106.15.2**.** -k
        ```

        系统输出类似如下结果：

        ```
        Hello Knative!
        ```

3.  将SLB的IP地址与Knative域名进行Host绑定，在Hosts文件中添加绑定信息。以下为绑定样例。

    **说明：** Knative默认的路由根域名是example.com，您可以自定义域名，更多信息，请参见[Knative自定义域名](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative服务管理/Knative自定义域名.md)。

    ```
    106.15.2**.** helloworld-go.default.example.com
    ```

    完成Hosts绑定后，如果您可以通过域名访问服务，表示Knative Gateway可以正常使用。

    ![1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3375621061/p170859.png)


