# 存储FAQ-Flexvolume

本文介绍您在使用存储卷时出现的常见问题。

-   [如何解决存储卷挂载不上的问题？](#section_dpb_h3t_h2b)
-   [如何查看存储相关日志？](#section_wrg_q3t_h2b)
-   [如何解决Kubelet出现不受ACK管理的Pod日志的问题？](#section_u3t_r77_ctv)

**云盘常见问题**

-   [云盘挂载失败，出现timeout错误](#p_9ip_0tb_o7e)
-   [云盘挂载失败，出现Size错误](#p_9ee_ydd_m8g)
-   [云盘挂载失败，出现zone错误](#b_jo1_rxy_3um)
-   [升级系统后，云盘报错input/output error](#p_8bx_120_0x5)
-   [卸载云盘时，提示The specified disk is not a portable](#p_753_h0p_4vv)
-   [启动挂载云盘的Pod时，提示had volume node affinity conflict](#p_lbx_etz_a0k)
-   [启动挂载了云盘的Pod时，提示can't find disk](#p_h54_zl8_8lx)
-   [动态创建PV失败，提示disk size is not supported](#b_0jl_4zq_rn1)

**NAS常见问题**

-   [NAS挂载时间太长](#p_02o_oix_evx)
-   [NAS挂载失败，出现timeouot错误](#p_gdi_g3x_fee)
-   [使用NAS时，提示chown: option not permitted](#p_r5b_5cr_i0p)
-   [挂载NAS PV失败](#p_959_byv_oy5)

**OSS常见问题**

-   [OSS挂载失败](#p_hck_sx6_if8)
-   [集群升级后，容器内OSS挂载目录不可用](#p_ndo_474_6sc)

## 如何解决存储卷挂载不上的问题？

您需要检查Flexvolume和动态存储插件是否安装，如果没有，请安装Flexvolume和动态存储插件。

**方式一：检查Flexvolume是否安装**

在Master节点上执行下面命令获取Pod信息。

```
kubectl get pod -n kube-system | grep flexvolume
```

返回结果如下：

```
flexvolume-4wh8s            1/1       Running   0          8d
flexvolume-65z49            1/1       Running   0          8d
flexvolume-bpc6s            1/1       Running   0          8d
flexvolume-l8pml            1/1       Running   0          8d
flexvolume-mzkpv            1/1       Running   0          8d
flexvolume-wbfhv            1/1       Running   0          8d
flexvolume-xf5cs            1/1       Running   0          8d   
```

查看Flexvolume Pod状态是否为Running，且运行的数量与节点数量相同。

如果运行状态不对，请参见插件运行日志分析。

**方式二：检查动态存储插件是否安装**

如果使用云盘的动态存储功能，需要确认是否安装动态存储插件，执行下面命令查看Pod信息。

```
kubectl get pod -n kube-system | grep alicloud-disk
```

返回结果如下：

```
alicloud-disk-controller-8679c9fc76-lq6zb     1/1 Running   0   7d           
```

如果运行状态不对，请参考插件运行日志分析。

## 如何查看存储相关日志？

您可以查看Flexvolume日志、Provsioner插件日志和Kubelet日志。

**方式一：查看Flexvolume日志（master1上执行）**

执行get命令查看出错的Pod。

```
kubectl get pod -n kube-system | grep flexvolume
```

执行log命令，查看出错Pod的日志。

```
kubectl logs flexvolume-4wh8s -n kube-system
kubectl describe pod flexvolume-4wh8s -n kube-system
```

**说明：** 在Pod描述最后若干行是Pod运行状态的描述，可以根据描述分析错误。

云盘、NAS、OSS驱动日志查看。

执行以下命令查看host节点上持久化的日志。如果某个Pod挂载失败，查看Pod所在的节点地址。

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

返回结果如下：

```
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.XX.XX
Node-Selectors:  <none>
```

登录节点，查看云盘、NAS、OSS挂载的日志。

```
ssh 192.168.XX.XX
ls /var/log/alicloud/flexvolume*
```

返回结果如下：

```
flexvolume_disk.log  flexvolume_nas.log  flexvolume_o#ss.log
```

**方式二：查看Provsioner插件日志（master1上执行）**

执行get命令查看出错的Pod。

```
kubectl get pod -n kube-system | grep alicloud-disk
```

执行log命令，查看出错Pod的日志。

```
kubectl logs alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
kubectl describe pod alicloud-disk-controller-8679c9fc76-lq6zb -n kube-system
```

**说明：** 在Pod描述最后若干行是Pod运行状态的描述，可以根据描述分析错误。

**方式三：查看Kubelet日志**

如果某个Pod挂载失败，查看Pod所在的节点地址。

```
kubectl describe pod nginx-97dc96f7b-xbx8t | grep Node
```

返回结果如下：

```
Node: cn-hangzhou.i-bp19myla3uvnt6zihejb/192.168.XX.XX
Node-Selectors:  <none>
```

登录节点，查看kubelet日志。

```
ssh 192.168.XX.XX
journalctl -u kubelet -r -n 1000 &> kubelet.log
```

**说明：** -n的值表示期望看到的日志行数。

上述为获取Flexvolume、Provsioner、Kubelet错误日志的方法，如果无法根据日志修复状态，可以附带日志信息联系阿里云技术支持。

## 如何解决Kubelet出现不受ACK管理的Pod日志的问题？

Pod异常退出，导致数据卷挂载点在卸载过程中没有清理干净，最终导致Pod无法删除。Kubelet的GC流程对数据卷垃圾回收实现并不完善，目前需要手动或脚本自动化实现垃圾挂载点的清理工作。

您需要在问题节点运行以下脚本，对垃圾挂载点进行清理。

```
wget https://raw.githubusercontent.com/AliyunContainerService/kubernetes-issues-solution/master/kubelet/kubelet.sh
sh kubelet.sh
```

## 云盘常见问题

**云盘挂载失败，出现timeout错误**

如果节点为手动添加，可能是由于STS权限的问题导致，需要手动配置RAM权限：[授予实例RAM角色](/cn.zh-CN/安全/实例RAM角色/授予实例RAM角色.md)。

**云盘挂载失败，出现Size错误**

创建云盘对Size有如下要求。

**说明：**

-   普通云盘：最小5 Gi
-   高效云盘：最小20 Gi
-   SSD云盘：最小20 Gi

**云盘挂载失败，出现zone错误**

ECS挂载云盘时，必须在同一个Region下面的相同zone内，否则不能挂载成功。

**升级系统后，云盘报错input/output error**

1.  升级Flexvolume到v1.9.7-42e8198或以后版本。
2.  对于已经出问题的Pod，需要重建。

升级命令：

```
kubectl set image daemonset/flexvolume acs-flexvolume=registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.9.7-42e8198 -n kube-system
```

Flexvolume版本信息：可登录容器镜像服务控制台，单击左侧导航栏中的**镜像搜索**，搜索acs/flexvolume，获取Flexvolume最新版本信息。

**卸载云盘时，提示The specified disk is not a portable disk**

您申请了包年包月的云盘，或者在升级ECS时，把ECS关联的云盘一起升级为包年包月。将云盘的付费方式改为按量付费即可解决该问题。

**启动挂载云盘的Pod时，出现Pod无法启动的情况并提示had volume node affinity conflict。**

您在PV中编写了nodeaffinity属性，这个属性与Pod中声明的属性不一致，导致Pod无法被调度到正确的节点上。您需要修改PV或者Pod的属性，使二者属性保持一致

**启动挂载了云盘的Pod时，提示can't find disk**

您在编写PV的时候输入了错误的diskid。或者您的账号无权限操作diskid，可能不是当前账号的diskid。您需要更换diskid。

**动态创建PV失败，提示disk size is not supported**

在PVC中指定的云盘大小不符合规范，要求最小20 Gi。您需要调整PVC中云盘的大小，使其符合规范。

## NAS常见问题

**NAS挂载时间太长**

如果NAS卷包含的文件量很大，且在挂载模板中配置了chmod参数，可能导致挂载时间过长的问题，您可以去掉chmod参数。

**NAS挂载失败，出现timeouot错误**

检查NAS挂载点和集群是否在同一个VPC内，否则无法挂载。

**使用NAS时，提示chown: option not permitted**

您的容器没有权限使用该NAS。您需要使用root权限启动容器。

**挂载NAS PV失败**

挂载NAS PV失败，并报以下错误：

```
Unable to mount volumes for pod "dp-earnings-pod_default(906172c6-3d68-11e8-86e0-00163e0084d3)": timeout expired waiting for volumes to attach/mount for pod "default"/"dp-earnings-pod". list of unattached/unmounted volumes=[vol1 vol2]
```

您没有安装Flexvolume插件。您需要安装Flexvolume插件。具体操作，请参见[安装与升级Flexvolume组件](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/安装与升级Flexvolume组件.md)。

## OSS常见问题

**OSS挂载失败**

-   检查使用的AK是否正确。

-   检查OSS挂载使用的URL是否网络可达。

**集群升级后，容器内OSS挂载目录不可用**

升级集群、重启kubelet的时候，由于容器网络会重启，导致ossfs进程重启。

ossfs进程重启会导致主机与容器目录映射失效，这时需要重启容器，或重建Pod。您可以通过配置健康检查实现容器或Pod的自动重启。

有关使用OSS存储的更多信息，请参见[OSS存储卷使用说明](/cn.zh-CN/Kubernetes集群用户指南/存储-Flexvolume/OSS存储卷/OSS存储卷使用说明.md)。

