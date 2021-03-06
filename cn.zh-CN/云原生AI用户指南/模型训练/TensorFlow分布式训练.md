---
keyword: [TensorFlow, 分布式训练, 共享存储系统, Arena]
---

# TensorFlow分布式训练

本文展示如何使用Arena提交TensorFlow基于PS-Worker模式的分布式训练作业，并通过TensorBoard可视化查看训练作业。

-   [创建包含GPU的Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/GPU调度/使用Kubernetes默认GPU调度.md)。
-   [集群节点可以访问公网](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过公网访问集群API Server.md)。
-   [安装Arena](/cn.zh-CN/解决方案/AI解决方案/环境准备/通过组件安装最新版的Arena.md)。
-   已给集群配置了Arena使用的PVC，并且PVC已填充本文使用的数据集，详情请参见[配置NAS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置NAS共享存储.md)（或者[配置CPFS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置CPFS共享存储.md)）。

本文示例从Git URL下载源代码，数据集放在共享存储系统（基于NAS的PV和PVC）中。示例假设您已经获得了一个名称为**training-data**的PVC实例（一个共享存储），里面存在一个目录tf\_data，存放了示例所使用的数据集。

1.  执行以下命令检查可用的GPU资源。

    ```
    arena top node
    ```

    预期输出：

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.16x.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    0/6 (0%)
    ```

    由上看出，有3个包含GPU的节点可用于运行训练作业。

2.  执行`arena submit tfjob/tf [--flag] command`形式命令提交TensorFlow作业。

    通过以下代码示例提交PS-Worker模式下的TensorFlow分布式作业，它包含1个PS节点，2个Worker节点。

    ```
        arena submit tf --name=tf-dist         \
                  --working-dir=/root \
                  --gpus=1              \
                  --workers=2              \
                  --worker-image=tensorflow/tensorflow:1.5.0-devel-gpu  \
                  --sync-mode=git \
                  --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
                  --ps=1              \
                  --ps-image=tensorflow/tensorflow:1.5.0-devel   \
                  --data=training-data:/mnist_data \
                  --tensorboard \
                  --logdir=/mnist_data/tf_data/logs \
                  "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --log_dir /mnist_data/tf_data/logs  --data_dir /mnist_data/tf_data/"
    ```

    预期输出：

    ```
    configmap/tf-dist-tfjob created
    configmap/tf-dist-tfjob labeled
    service/tf-dist-tensorboard created
    deployment.apps/tf-dist-tensorboard created
    tfjob.kubeflow.org/tf-dist created
    INFO[0000] The Job tf-dist has been submitted successfully
    INFO[0000] You can run `arena get tf-dist --type tfjob` to check the job status
    ```

    参数解释如下表。

    |参数|是否必选|解释|默认值|
    |--|----|--|---|
    |--name|必选|指定提交的作业名字，全局唯一，不能重复。|无|
    |--working-dir|可选|指定当前执行命令所在的目录。|/root|
    |--gpus|可选|指定作业Worker节点需要使用的GPU有卡数。|0|
    |--workers|可选|指定作业Worker节点的数量。|1|
    |--image|如果不单独指定--worker-image、--ps-image，则必选。|指定训练环境的镜像地址。如果不指定--worker-image或者--ps-image，则Worker节点和PS节点都使用该镜像地址。|无|
    |--worker-image|如果不指定--image，则必选。|指定作业Worker节点需要使用的镜像地址。如果--image同时出现，则覆盖--image。|无|
    |--sync-mode|可选|同步代码的模式，您可以指定git、rsync。本文使用git模式。|无|
    |--sync-source|可选|同步代码的仓库地址，需要和--sync-mode一起使用。本文示例使用git模式，该参数可以为任何github项目地址。阿里云code项目地址等支持git的代码托管地址。项目代码将会被下载到--working-dir下的code/目录中。本文示例即为：/root/code/tensorflow-sample-code。|无|
    |--ps|分布式作业必选|指定参数服务器PS节点数。|0|
    |--ps-image|如果不指定--image，则必选。|指定PS节点的镜像地址。如果--image同时出现，则覆盖--image。|无|
    |--data|可选|挂载共享存储卷PVC到运行环境中。它由两部分组成，通过冒号（`:`）分割。冒号左侧是您已经准备好的PVC名称。您可以通过命令`arena data list`查看当前集群可用的PVC列表；冒号右侧是您想将PVC的挂载到运行环境中的路径，也是您训练代码要读取数据的本地路径。这样通过挂载的方式，您的代码就可以访问PVC的数据。**说明：** 执行`arena data list`查看本文示例当前集群可用的PVC列表。

    ```
NAME           ACCESSMODE     DESCRIPTION  OWNER  AGE
training-data  ReadWriteMany                      35m
    ```

如果没有可用的PVC，您可创建PVC。详情请参见[配置NAS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置NAS共享存储.md)（或者[配置CPFS共享存储](/cn.zh-CN/解决方案/AI解决方案/环境准备/配置CPFS共享存储.md)）。

|无|
    |--tensorboard|可选|为训练任务开启一个TensoBoard服务，用作数据可视化，您可以结合--logdir指定TensorBoard要读取的event路径。不指定该参数，则不开启TensorBoard服务。|无|
    |--logdir|可选|需要结合--tensorboard一起使用，该参数表示TensorBoard需要读取event数据的路径。|/training\_logs|

    **说明：** 如果您使用非公开Git代码库，则可以使用以下命令。

    ```
     arena submit tf \
            ...
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
            --env=GIT_SYNC_USERNAME=yourname \
            --env=GIT_SYNC_PASSWORD=yourpwd \
            "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py
    ```

    arena命令使用[git-sync](https://github.com/kubernetes/git-sync/blob/master/cmd/git-sync/main.go)同步源代码。您可以设置在git-sync项目中定义的环境变量。

3.  执行以下命令查看当前通过Arena提交的所有作业。

    ```
    arena list
    ```

    预期输出：

    ```
    NAME     STATUS     TRAINER  AGE  NODE
    tf-dist  RUNNING    TFJOB    58s  192.1xx.x.xx
    tf-git   SUCCEEDED  TFJOB    2h   N/A
    ```

4.  执行以下命令检查作业所使用的GPU资源。

    ```
    arena top job
    ```

    预期输出：

    ```
    NAME     GPU(Requests)  GPU(Allocated)  STATUS     TRAINER  AGE  NODE
    tf-dist  2              2               RUNNING    tfjob    1m   192.1xx.x.x
    tf-git   1              0               SUCCEEDED  tfjob    2h   N/A
    
    
    Total Allocated GPUs of Training Job:
    2
    
    Total Requested GPUs of Training Job:
    3
    ```

5.  执行以下命令检查集群所使用的GPU资源。

    ```
    arena top node
    ```

    预期输出：

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    2/6 (33%)
    ```

6.  执行以下命令获取作业详情。

    ```
    arena get tf-dist
    ```

    预期输出：

    ```
    STATUS: RUNNING
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 1m
    
    NAME     STATUS   TRAINER  AGE  INSTANCE          NODE
    tf-dist  RUNNING  TFJOB    1m   tf-dist-ps-0      192.1xx.x.xx
    tf-dist  RUNNING  TFJOB    1m   tf-dist-worker-0  192.1xx.x.xx
    tf-dist  RUNNING  TFJOB    1m   tf-dist-worker-1  192.1xx.x.xx
    
    Your tensorboard will be available on:
    http://192.1xx.x.xx:31870
    ```

    **说明：** 本文示例因为开启TensorBoard，在上述作业详情中最后两行，可以看到TensorBoard的web访问地址；如果没有开启TensorBoard，最后两行信息不存在。

7.  通过浏览器查看TensorBoard。

    从上述步骤的作业详情中，可以看到TensorBoard的web服务地址。由于集群为远端部署，因此需要使用sshuttle代理才能在您的电脑中通过浏览器查看训练可视化的信息。

    使用sshuttle代理示例代码如下。

    ```
    # you can install sshuttle==0.74 in your mac with python2.7
    pip install sshuttle==0.74
    # 0/0 -> 0.0.0.0/0
    sshuttle -r root@39.104.xx.xxx  0/0
    ```

    **说明：** 39.104.xx.xxx为ACK集群对外暴露的公网IP地址。此外，您还需检查您的安全组是否开启了22端口，一般默认开启。

    将步骤6中获取的TensorBoard的web服务地址http://192.1xx.x.xx:31870，拷贝至浏览器地址栏，显示效果如下图。

    ![tf](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6704359951/p135005.png)

8.  执行以下命令获取作业日志信息。

    ```
    arena logs tf-dist
    ```

    预期输出：

    ```
    WARNING:tensorflow:From code/tensorflow-sample-code/tfjob/docker/mnist/main.py:120: softmax_cross_entropy_with_logits (from tensorflow.python.ops.nn_ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    ...
    Accuracy at step 960: 0.9691
    Accuracy at step 970: 0.9677
    Accuracy at step 980: 0.9687
    Accuracy at step 990: 0.968
    Adding run metadata for 999
    Total Train-accuracy=0.968
    ```

    使用上述命令获取作业日志信息时，默认输出worker-0的节点日志。如果需要查看分布式训练任务中的某个节点日志，可以先查看作业详情获取作业的节点列表，然后使用命令`arena logs $job_name -i $instance_name`查看具体实例的日志。

    示例代码如下。

    ```
    arena get tf-dist
    ```

    预期输出：

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 1m
    
    NAME     STATUS     TRAINER  AGE  INSTANCE          NODE
    tf-dist  SUCCEEDED  TFJOB    5m   tf-dist-ps-0      192.16x.x.xx
    tf-dist  SUCCEEDED  TFJOB    5m   tf-dist-worker-0  192.16x.x.xx
    tf-dist  SUCCEEDED  TFJOB    5m   tf-dist-worker-1  192.16x.x.xx
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:31870
    ```

    执行以下命令获取作业日志。

    ```
    arena logs tf-dist -i tf-dist-worker-1
    ```

    预期输出：

    ```
    WARNING:tensorflow:From code/tensorflow-sample-code/tfjob/docker/mnist/main.py:120: softmax_cross_entropy_with_logits (from tensorflow.python.ops.nn_ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    ...
    Accuracy at step 970: 0.9676
    Accuracy at step 980: 0.968
    Accuracy at step 990: 0.967
    Adding run metadata for 999
    Total Train-accuracy=0.967
    ```

    您还可以通过命令`arena logs $job_name -f`实时查看作业的日志输出，通过命令`arena logs $job_name -t N`查看尾部N行的日志，以及通过`arena logs --help`查询更多参数使用情况。

    查看尾部N行的日志示例代码如下。

    ```
    arena logs tf-dist -t 5
    ```

    预期输出：

    ```
    Accuracy at step 9970: 0.9834
    Accuracy at step 9980: 0.9828
    Accuracy at step 9990: 0.9816
    Adding run metadata for 9999
    Total Train-accuracy=0.9816
    ```


