# 通过YAML使用云盘静态存储卷

本文主要为您介绍如何在CSI插件中，通过YAML的方式挂载云盘静态存储卷。

-   您已经创建好一个Kubernetes集群，并且在该集群中部署CSI插件。
-   您已经创建好一个按量付费的云盘。请参见[创建云盘](/cn.zh-CN/块存储/云盘基础操作/创建云盘/创建云盘.md)。

    **说明：** 申请云盘时，请遵循如下容量限制：

    -   高效云盘：最小20 GiB。
    -   SSD云盘：最小20 GiB。
    -   ESSD云盘：最小20 GiB。

## 创建静态PV和PVC

通过云盘控制台创建云盘后，记录其DiskId为`d-wz92s6d95go6ki9xge6b`。

目前控制台只支持通过YAML模板创建CSI PV对象。

1.  [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群管理/连接集群/通过kubectl连接Kubernetes集群.md)。

2.  通过下面模板创建静态卷PV和PVC。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: csi-pvc
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 25Gi
      selector:
        matchLabels:
          alicloud-pvname: static-disk-pv
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: csi-pv
      labels:
        alicloud-pvname: static-disk-pv
    spec:
      capacity:
        storage: 25Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: diskplugin.csi.alibabacloud.com
        volumeHandle: d-wz92s6d95go6ki9xge6b
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.diskplugin.csi.alibabacloud.com/zone
              operator: In
              values:
              - cn-shenzhen-a
    ```

    **说明：**

    -   driver：定义驱动类型。

        取值为`diskplugin.csi.alibabacloud.com`，表示使用阿里云云盘CSI插件。

    -   volumeHandle：定义云盘ID。
    -   nodeAffinity：定义PV/PVC所属的Zone信息。

        通过定义该参数，可以将PV/PVC所在的Pod调度到对应的Zone上。


## 创建应用

1.  创建并拷贝以下内容到nginx-disk-dept.yaml文件中。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
              name: web
            volumeMounts:
            - name: pvc-disk
              mountPath: /data
          volumes:
            - name: pvc-disk
              persistentVolumeClaim:
                claimName: csi-pvc
    ```

2.  执行以下命令，创建一个应用。

    ```
    kubectl apply -f nginx-disk-dept.yaml
    ```


您也可以通过控制台的方式使用云盘静态存储卷，请参见[通过控制台使用云盘静态存储卷](/cn.zh-CN/Kubernetes集群用户指南/存储管理-CSI/云盘存储卷/通过控制台使用云盘静态存储卷.md)。

