---
keyword: [云盘静态存储卷, 持久化存储]
---

# 使用云盘静态存储卷实现持久化存储-Flexvolume

当容器发生宕机故障时，有状态服务容器存储的业务数据存在着丢失和不可靠等风险。使用持久化存储可以解决该问题。本文介绍如何使用云盘静态存储卷实现持久化存储。

请确保您已完成以下操作：

-   [创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)
-   [创建云盘](/cn.zh-CN/块存储/云盘基础操作/创建云盘/创建云盘.md)
-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)

云盘的使用场景包括：

-   对磁盘I/O要求高的应用，且没有共享数据的需求，如MySQL、Redis等数据存储服务。
-   高速写日志。
-   持久化存储数据，不因Pod生命周期的结束而消失。

静态云盘的使用场景：已经购买了云盘实例的情况。

静态云盘使用方式：需手动创建PV及PVC。请参见[使用静态云盘卷](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/云盘存储卷/使用静态云盘卷.md)。

## 使用限制

-   云盘为阿里云存储团队提供的非共享存储，只能同时被一个Pod挂载。
-   集群中只有与云盘在同一个可用区（Zone）的节点才可以挂载云盘。

## 创建PV

1.  创建pv-static.yaml文件。

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: <your-disk-id>
      labels:
        alicloud-pvname: <your-disk-id>
        failure-domain.beta.kubernetes.io/zone: <your-zone>
        failure-domain.beta.kubernetes.io/region: <your-region>
    spec:
      capacity:
        storage: 20Gi
      accessModes:
        - ReadWriteOnce
      flexVolume:
        driver: "alicloud/disk"
        fsType: "ext4"
        options:
          volumeId: "<your-disk-id>"
    ```

    **说明：**

    -   `alicloud-pvname: <your-disk-id>`：PV的名称。需要与云盘ID一致。
    -   `failure-domain.beta.kubernetes.io/zone: <your-zone>`：云盘所在的可用区。例如：cn-hangzhou-b。
    -   `failure-domain.beta.kubernetes.io/region: <your-region>`：云盘所在的地域。例如：cn-hangzhou。
    `failure-domain.beta.kubernetes.io/zone`和`failure-domain.beta.kubernetes.io/region`，在多可用区的情况下，必须配置，可保证您的Pod调度到云盘所在的可用区。

2.  执行以下命令，创建PV。

    ```
    kubectl create -f pv-static.yaml
    ```

    **验证结果**

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏中，单击**存储** \> **存储卷**，可以看到刚刚创建的PV。

        ![创建PV](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3123319951/p158148.png)


## 创建PVC

1.  创建pvc-static.yaml文件。

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: pvc-disk
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
      selector:
        matchLabels:
          alicloud-pvname: <your-disk-id>
    ```

2.  执行以下命令，创建PVC。

    ```
    kubectl create -f pvc-static.yaml
    ```

    **验证结果**

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏中，单击**存储** \> **存储声明**。
    5.  在**存储声明**页面，可以看到刚刚创建的PVC。

## 创建应用

1.  创建static.yaml文件。

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-static
      labels:
        app: nginx
    spec:
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            volumeMounts:
              - name: disk-pvc
                mountPath: "/data"
          volumes:
            - name: disk-pvc
              persistentVolumeClaim:
                claimName: pvc-disk
    ```

2.  执行以下命令，创建静态应用。

    ```
    kubectl create -f static.yaml
    ```

    **验证结果**

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
    2.  在控制台左侧导航栏中，单击**集群**。
    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
    4.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。
    5.  在**无状态**页面，可以看到刚刚创建的静态应用。

## 静态云盘的持久化存储

1.  执行以下命令，查看部署的静态应用所在Pod的名称。

    ```
    kubectl get pod | grep static
    ```

    预期输出：

    ```
    nginx-static-78c7dcb9d7-g****   2/2     Running     0          32s
    ```

2.  执行以下命令，查看/data路径下是否挂载了新的云盘。

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** df | grep data
    ```

    预期输出：

    ```
    /dev/vdf        20511312    45080  20449848   1% /data
    ```

3.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** ls /data
    ```

    预期输出：

    ```
    lost+found
    ```

4.  执行以下命令，在/data路径下创建文件static。

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** touch /data/static
    ```

5.  执行以下命令，查看/data路径下的文件。

    ```
    kubectl exec nginx-static-78c7dcb9d7-g**** ls /data
    ```

    预期输出：

    ```
    static
    lost+found
    ```

6.  执行以下命令，删除名称为`nginx-static-78c7dcb9d7-g****`的Pod。

    ```
    kubectl delete pod nginx-static-78c7dcb9d7-g****
    ```

    预期输出：

    ```
    pod "nginx-static-78c7dcb9d7-g****" deleted
    ```

7.  同时在另一个窗口中，执行以下命令，查看Pod删除及Kubernetes重建Pod的过程。

    ```
    kubectl get pod -w -l app=nginx
    ```

    预期输出：

    ```
    NAME                            READY   STATUS            RESTARTS   AGE
    nginx-static-78c7dcb9d7-g****   2/2     Running           0          50s
    nginx-static-78c7dcb9d7-g****   2/2     Terminating       0          72s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Pending           0          0s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          0s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          73s
    nginx-static-78c7dcb9d7-h****   0/2     Init:0/1          0          5s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-g****   0/2     Terminating       0          78s
    nginx-static-78c7dcb9d7-h****   0/2     PodInitializing   0          6s
    nginx-static-78c7dcb9d7-h****   2/2     Running           0          8s
    ```

8.  执行以下命令，查看Kubernetes重建的Pod名称。

    ```
    kubectl get pod
    ```

    预期输出：

    ```
    NAME                            READY   STATUS      RESTARTS   AGE
    nginx-static-78c7dcb9d7-h****   2/2     Running     0          14s
    ```

9.  执行以下命令，查看/data路径下的文件，刚刚创建的文件static并没有被删除，说明静态云盘的数据可持久保存。

    ```
    kubectl exec nginx-static-78c7dcb9d7-h6brd ls /data
    ```

    预期输出：

    ```
    static
    lost+found
    ```


