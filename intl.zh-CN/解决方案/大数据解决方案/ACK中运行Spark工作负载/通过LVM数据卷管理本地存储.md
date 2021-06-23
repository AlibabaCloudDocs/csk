---
keyword: [LVM数据卷, 格式化, 简化数据盘挂载]
---

# 通过LVM数据卷管理本地存储

在大数据场景下，选用ecs.d1ne.6xlarge规格的ECS实例运行Spark作业。每个实例自带12块5 TB的HDD数据盘，需要对这12个数据盘进行手动分区格式化和挂载。当节点数量多时，手动分区格式化和挂载是一个十分繁琐和耗时的操作。本文介绍如何通过LVM数据卷对数据盘格式化和挂载进行简化。

-   [创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   集群节点的数据盘设备未经过手动格式化和挂载。

## 部署LVM插件



## 配置PVC

1.  使用以下模板配置StorageClass。

    ```
    allowVolumeExpansion: true
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: csi-local-imm
    parameters:
      fsType: ext4
      lvmType: striping
      vgName: volumegroup1
      volumeType: LVM
    provisioner: localplugin.csi.alibabacloud.com
    reclaimPolicy: Delete
    volumeBindingMode: Immediate
    ```

2.  使用以下模板配置PVC。

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: lvm-pvc-im
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 12288Gi
      storageClassName: csi-local-imm
    ```


## 配置Alluxio

使用以下模板配置Alluxio。

```
# The Alluxio Open Foundation licenses this work under the Apache License, version 2.0
# (the "License"). You may not use this work except in compliance with the License, which is
# available at www.apache.org/licenses/LICENSE-2.0
#
# This software is distributed on an "AS IS" basis, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
# either express or implied, as more fully set forth in the License.
#
# See the NOTICE file distributed with this work for information regarding copyright ownership.
#

# This should not be modified in the usual case.
fullnameOverride: alluxio


## Common ##

# Docker Image
image: registry-vpc.cn-beijing.aliyuncs.com/alluxio/alluxio
imageTag: 2.3.0
imagePullPolicy: IfNotPresent

# Security Context
user: 0
group: 0
fsGroup: 0

# Site properties for all the components
properties:
  fs.oss.accessKeyId: YOUR-ACCESS-KEY-ID
  fs.oss.accessKeySecret: YOUR-ACCESS-KEY-SECRET
  fs.oss.endpoint: oss-cn-beijing-internal.aliyuncs.com
  alluxio.master.mount.table.root.ufs: oss://cloudnativeai/
  alluxio.master.persistence.blacklist: .staging,_temporary
  alluxio.security.stale.channel.purge.interval: 365d
  alluxio.user.metrics.collection.enabled: 'true'
  alluxio.user.short.circuit.enabled: 'true'
  alluxio.user.file.write.tier.default: 1
  alluxio.user.block.size.bytes.default: 64MB #default 64MB
  alluxio.user.file.writetype.default: CACHE_THROUGH
  alluxio.user.file.metadata.load.type: ONCE
  alluxio.user.file.readtype.default: CACHE
  #alluxio.worker.allocator.class: alluxio.worker.block.allocator.MaxFreeAllocator
  alluxio.worker.allocator.class: alluxio.worker.block.allocator.RoundRobinAllocator
  alluxio.worker.file.buffer.size: 128MB
  alluxio.worker.evictor.class: alluxio.worker.block.evictor.LRUEvictor
  alluxio.job.master.client.threads: 5000
  alluxio.job.worker.threadpool.size: 300

# Recommended JVM Heap options for running in Docker
# Ref: https://developers.redhat.com/blog/2017/03/14/java-inside-docker/
# These JVM options are common to all Alluxio services
# jvmOptions:
#   - "-XX:+UnlockExperimentalVMOptions"
#   - "-XX:+UseCGroupMemoryLimitForHeap"
#   - "-XX:MaxRAMFraction=2"

# Mount Persistent Volumes to all components
# mounts:
# - name: <persistentVolume claimName>
#   path: <mountPath>

# Use labels to run Alluxio on a subset of the K8s nodes

## Master ##

master:
  count: 1 # Controls the number of StatefulSets. For multiMaster mode increase this to >1.
  replicas: 1 # Controls #replicas in a StatefulSet and should not be modified in the usual case.
  enableLivenessProbe: false
  enableReadinessProbe: false
  args: # Arguments to Docker entrypoint
    - master-only
    - --no-format
  # Properties for the master component
  properties:
  # Example: use ROCKS DB instead of Heap
  # alluxio.master.metastore: ROCKS
  # alluxio.master.metastore.dir: /metastore
  resources:
    # The default xmx is 8G
    limits:
      cpu: "4"
      memory: "8G"
    requests:
      cpu: "1"
      memory: "1G"
  ports:
    embedded: 19200
    rpc: 19998
    web: 19999
  hostPID: true
  hostNetwork: true
  # dnsPolicy will be ClusterFirstWithHostNet if hostNetwork: true
  # and ClusterFirst if hostNetwork: false
  # You can specify dnsPolicy here to override this inference
  # dnsPolicy: ClusterFirst
  # JVM options specific to the master container
  jvmOptions:
  nodeSelector:
    alluxio: 'true'

jobMaster:
  args:
    - job-master
  # Properties for the jobMaster component
  enableLivenessProbe: false
  enableReadinessProbe: false
  properties:
  resources:
    limits:
      cpu: "4"
      memory: "8G"
    requests:
      cpu: "1"
      memory: "1G"
  ports:
    embedded: 20003
    rpc: 20001
    web: 20002
  # JVM options specific to the jobMaster container
  jvmOptions:

# Alluxio supports journal type of UFS and EMBEDDED
# UFS journal with HDFS example
# journal:
#   type: "UFS"
#   folder: "hdfs://{$hostname}:{$hostport}/journal"
# EMBEDDED journal to /journal example
# journal:
#   type: "EMBEDDED"
#   folder: "/journal"
journal:
  type: "UFS" # "UFS" or "EMBEDDED"
  ufsType: "local" # Ignored if type is "EMBEDDED". "local" or "HDFS"
  folder: "/journal" # Master journal folder
  # volumeType controls the type of journal volume.
  # It can be "persistentVolumeClaim" or "emptyDir"
  volumeType: emptyDir
  size: 1Gi
  # Attributes to use when the journal is persistentVolumeClaim
  storageClass: "standard"
  accessModes:
    - ReadWriteOnce
  # Attributes to use when the journal is emptyDir
  medium: ""
  # Configuration for journal formatting job
  format:
    runFormat: false # Change to true to format journal
    job:
      activeDeadlineSeconds: 30
      ttlSecondsAfterFinished: 10
    resources:
      limits:
        cpu: "4"
        memory: "8G"
      requests:
        cpu: "1"
        memory: "1G"


# You can enable metastore to use ROCKS DB instead of Heap
# metastore:
#   volumeType: persistentVolumeClaim # Options: "persistentVolumeClaim" or "emptyDir"
#   size: 1Gi
#   mountPath: /metastore
# # Attributes to use when the metastore is persistentVolumeClaim
#   storageClass: "standard"
#   accessModes:
#    - ReadWriteOnce
# # Attributes to use when the metastore is emptyDir
#   medium: ""


## Worker ##

worker:
  args:
    - worker-only
    - --no-format
  enableLivenessProbe: false
  enableReadinessProbe: false
  # Properties for the worker component
  properties:
  resources:
    limits:
      cpu: "4"
      memory: "4G"
    requests:
      cpu: "1"
      memory: "2G"
  ports:
    rpc: 29999
    web: 30000
  hostPID: true
  hostNetwork: true
  # dnsPolicy will be ClusterFirstWithHostNet if hostNetwork: true
  # and ClusterFirst if hostNetwork: false
  # You can specify dnsPolicy here to override this inference
  # dnsPolicy: ClusterFirst
  # JVM options specific to the worker container
  jvmOptions:
  nodeSelector:
    alluxio: 'true'

jobWorker:
  args:
    - job-worker
  enableLivenessProbe: false
  enableReadinessProbe: false
  # Properties for the jobWorker component
  properties:
  resources:
    limits:
      cpu: "4"
      memory: "4G"
    requests:
      cpu: "1"
      memory: "1G"
  ports:
    rpc: 30001
    data: 30002
    web: 30003
  # JVM options specific to the jobWorker container
  jvmOptions:

# Tiered Storage
# emptyDir example
#  - level: 0
#    alias: MEM
#    mediumtype: MEM
#    path: /dev/shm
#    type: emptyDir
#    quota: 1G
#
# hostPath example
#  - level: 0
#    alias: MEM
#    mediumtype: MEM
#    path: /dev/shm
#    type: hostPath
#    quota: 1G
#
# persistentVolumeClaim example
#  - level: 1
#    alias: SSD
#    mediumtype: SSD
#    type: persistentVolumeClaim
#    name: alluxio-ssd
#    path: /dev/ssd
#    quota: 10G
#
# multi-part mediumtype example
#  - level: 1
#    alias: SSD,HDD
#    mediumtype: SSD,HDD
#    type: persistentVolumeClaim
#    name: alluxio-ssd,alluxio-hdd
#    path: /dev/ssd,/dev/hdd
#    quota: 10G,10G
tieredstore:
  levels:
    - level: 0
      alias: HDD
      mediumtype: HDD-0
      path: /mnt/disk1
      type: persistentVolumeClaim
      name: lvm-pvc-im
      quota: 12000G
      high: 0.95
      low: 0.7

# Short circuit related properties
shortCircuit:
  enabled: true
  # The policy for short circuit can be "local" or "uuid",
  # local means the cache directory is in the same mount namespace,
  # uuid means interact with domain socket
  policy: uuid
  # volumeType controls the type of shortCircuit volume.
  # It can be "persistentVolumeClaim" or "hostPath"
  volumeType: hostPath
  size: 1Mi
  # Attributes to use if the domain socket volume is PVC
  pvcName: alluxio-worker-domain-socket
  accessModes:
    - ReadWriteOnce
  storageClass: standard
  # Attributes to use if the domain socket volume is hostPath
  hostPath: "/tmp/alluxio-domain" # The hostPath directory to use


## FUSE ##

fuse:
  image: registry-vpc.cn-beijing.aliyuncs.com/alluxio/alluxio-fuse
  imageTag: 2.3.0
  imagePullPolicy: IfNotPresent
  # Change both to true to deploy FUSE
  enabled: false
  clientEnabled: false
  # Properties for the jobWorker component
  properties:
  # Customize the MaxDirectMemorySize
  # These options are specific to the FUSE daemon
  jvmOptions:
    - "-XX:MaxDirectMemorySize=2g"
  hostNetwork: true
  hostPID: true
  dnsPolicy: ClusterFirstWithHostNet
  user: 0
  group: 0
  fsGroup: 0
  args:
    - fuse
    - --fuse-opts=allow_other
  # Mount path in the host
  mountPath: /mnt/alluxio-fuse
  resources:
    requests:
      cpu: "0.5"
      memory: "1G"
    limits:
      cpu: "4"
      memory: "4G"
  nodeSelector:
    alluxio: 'true'


##  Secrets ##

# Format: (<name>:<mount path under /secrets/>):
# secrets:
#   master: # Shared by master and jobMaster containers
#     alluxio-hdfs-config: hdfsConfig
#   worker: # Shared by worker and jobWorker containers
#     alluxio-hdfs-config: hdfsConfig
```

**说明：**

-   需使用上述配置的PVC配置tieredstore。

    ```
    tieredstore:
      levels:
        - level: 0
          alias: HDD
          mediumtype: HDD-0
          path: /mnt/disk1
          type: persistentVolumeClaim
          name: lvm-pvc-im
          quota: 12000G
          high: 0.95
          low: 0.7
    ```

-   Alluxio需要和PVC在同一个namspace下。

**相关文档**  


[LVM数据卷](/intl.zh-CN/Kubernetes集群用户指南/存储-CSI/本地存储卷/LVM数据卷.md)

[搭建测试环境](/intl.zh-CN/解决方案/大数据解决方案/ACK中运行Spark工作负载/搭建测试环境.md)

