---
keyword: [HTTPS安全访问, HTTPS访问配置]
---

# 在Kubernetes中实现HTTPS安全访问

当前容器服务Kubernetes集群支持多种应用访问的形式，最常见形式如`SLB:Port`、`NodeIP:NodePort`和域名访问等。Kubernetes集群默认不支持HTTPS访问，如果用户希望能够通过HTTPS进行应用的访问，容器服务和阿里云负载均衡服务为您提供安全的HTTPS访问。本文旨在通过实际案例演示的HTTPS访问配置，帮助用户在容器服务Kubernetes中配置自己的证书。

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/创建集群/创建Kubernetes托管版集群.md)。
-   [SSH访问Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过SSH访问Kubernetes集群.md)。
-   连接到Master节点后，创建集群的服务器证书，包括公钥证书和私钥。您可通过以下命令快速创建。

    ```
    openssl genrsa -out tls.key 2048
    ```

    输出：

    ```
    Generating RSA private key, 2048 bit long modulus
    ................................................................+++
    ........................................................................................+++
    e is 65537 (0x10001)
    ```

    ```
    openssl req -sha256 -new -x509 -days 365 -key tls.key -out tls.crt
    ```

    输出：

    ```
    You are about to be asked to enter information that will be incorporated
    ...
    -----
    Country Name (2 letter code) [XX]:CN
    State or Province Name (full name) []:zhejiang
    Locality Name (eg, city) [Default City]:hangzhou
    Organization Name (eg, company) [Default Company Ltd]:alibaba
    Organizational Unit Name (eg, section) []:test
    Common Name (eg, your name or your server's hostname) []:foo.bar.com           #注意，您需要正确配置域名。
    Email Address []:a@alibaba.com
    ```


根据访问的方式不同，当前可以分为两种配置证书的方式：

-   在前端SLB上配置证书。
-   在Ingress中配置证书。

## 在SLB上配置HTTPS证书

该方式有如下特点：

-   优点：证书配置在SLB上，为应用外部访问的入口，在集群内部进行应用的访问依然用的是http访问方式。
-   缺点：需要维护较多的域名与IP地址的对应关系。
-   适用场景：应用不使用Ingress暴露访问方式，通过LoadBalancer类型的Service进行应用访问的暴露。

准备工作：

您已在该Kubernetes集群中创建一个Tomcat应用，该应用采用LoadBalancer类型的服务（service）对外提供访问。详情请参见[创建服务](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Service管理/创建服务.md)。

示例：

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

5.  选择集群的命名空间和服务，单击外部端点，您可通过`IP:Port`的方式访问该应用。

    ![外部端点](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9095659951/p13806.png)

6.  登录[负载均衡管理控制台](https://slb.console.aliyun.com/)。

7.  在控制台左侧导航栏，选择**传统型负载均衡CLB** \> **实例管理**，在服务地址栏中，找到与tomcat-svc服务外部端点对应的负载均衡实例，单击操作列中的**监听配置向导**。

8.  开始进行负载均衡配置，首先进行配置监听协议。选择**HTTPS**，监听端口设置为**443**，然后单击**下一步**。

9.  配置**SSL证书**。

    1.  首先单击**新建服务器证书**。

    2.  在弹出的创建证书页面中，选择证书来源。本例中选择**上传非阿里云签发证书**，然后配置证书名称，选择证书部署区域，然后在**公钥证书**和**私钥**栏中输入您创建的服务器公钥证书和私钥，最后单击**创建**。

    3.  然后在**选择服务器证书**栏选择刚创建的服务器证书。

    4.  单击**下一步**。

10. 配置**后端服务器**，默认情况下已添加服务器，您需要配置后端服务器**端口**，用于监听tomcat-svc服务，最后单击**下一步**。

    **说明：** 您需要在容器服务Web界面找到该服务对应的NodePort，并在后端服务器端口中配置该端口。

11. 配置**健康检查**，然后单击**下一步**。本例中采用默认配置。

12. 进行**配置审核**，确认配置正确后，单击**提交**。

13. 配置成功后，单击**确定**。

14. 返回实例管理页面，您查看该实例，`HTTPS:443`监听规则已经生成。

15. 访问HTTPS的tomcat应用，在浏览器中输入`https://slb_ip`并进行访问。

    **说明：** 如果在证书中加入了域名验证，可以使用域名进行访问。同时我们没有删除`tcp:8080`，所以通过`slb_ip:8080`也可以访问。

    ![实例管理](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0195659951/p13820.png)


## 在Ingress上配置证书

该方法有以下特点：

-   优点：无需改动SLB的配置。每一个应用都可以通过Ingress管理自己的证书，互不干扰。
-   适用场景：每个应用都需要单独的证书进行访问，或者集群中存在需要证书才能访问的应用。

准备工作：

您已在该Kubernetes集群中创建一个Tomcat应用，该应用的服务（Service）采用ClusterIP的方式提供访问。本例中准备使用Ingress对外提供HTTPS访问服务。

示例：

1.  登录到Kubernetes集群的Master节点，根据准备好的证书创建secret。

    **说明：** 在这里需要正确配置域名，否则后续通过HTTPS访问会有问题。

    ```
    kubectl create secret tls secret-https --key tls.key --cert tls.crt      
    ```

2.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

3.  在控制台左侧导航栏中，单击**集群**。

4.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

5.  在集群管理页左侧导航栏中，选择**命名空间与配额**，单击右上角**创建**。

6.  在创建路由对话框中，配置可HTTPS访问的路由，完成后单击**创建**。

    更多详细的路由配置信息，请参见[创建路由（Ingress）](/cn.zh-CN/Kubernetes集群用户指南/网络管理/Ingress管理/创建路由（Ingress）.md)。本例中进行如下配置。

    -   **名称**：输入该路由的名称。
    -   **域名**：即是前面配置的正确域名，与ssl证书中配置的保持一致。
    -   **服务**：选择tomcat应用对应的service，端口为8080。
    -   **开启TLS**：开启TLS后，选择已创建的secret。
    您也可采用YAML文件的方式创建路由（Ingress），本例对应的YAML示例文件如下。

    ```
    apiVersion: extensions/v1beta1
    
    kind: Ingress
    
    metadata:
    
       name: tomcat-https
    
    spec:
    
      tls:
    
      - hosts:
    
        - foo.bar.com
    
        secretName: secret-https
    
      rules:
    
      - host: foo.bar.com
    
        http:
    
          paths:
    
          - path: /
    
            backend:
    
              serviceName: tomcat-svc
    
              servicePort: 8080
    ```

7.  返回路由列表，查看创建的路由（Ingress），本例中域名为`foo.bar.com`，并查看端点和域名，您也可进入路由详情页进行查看。

    **说明：** 本例中以`foo.bar.com`作为测试域名，您需要在hosts文件中创建一条记录。

    ```
    47.110.119.203  foo.bar.com                   #其中IP地址即是路由的端点。
    ```

    ![路由](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0195659951/p161116.png)

8.  在浏览器中访问`https://foo.bar.com`。

    **说明：** 由于创建了TLS证书访问，所以要用HTTPS来进行域名访问，针对该应用，本例以`foo.bar.com`为示例，在本地进行解析。在具体使用场景中，请使用备案过的域名。

    ![路由访问](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/0195659951/p13837.png)


