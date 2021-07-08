---
keyword: [PyTorch, single-machine training, shared file system, Arena]
---

# Use Arena to submit standalone PyTorch training jobs

This topic describes how to use the Arena client to submit standalone PyTorch jobs and use TensorBoard to visualize training results.

-   [A cluster of Alibaba Cloud Container Service for Kubernetes \(ACK\) that contains GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/GPU scheduling/Use GPU scheduling for ACK clusters.md).
-   [Nodes in the cluster can access the Internet](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Access the Kubernetes API server over the Internet.md).
-   [Install Arena]().
-   A persistent volume claim \(PVC\) is created for the target ACK cluster and the datasets used in this topic are downloaded to the corresponding persistent volume \(PV\). For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume.md) or [Configure a shared CPFS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared CPFS volume.md).

In this topic, the source training code is downloaded from a Git repository. The datasets are stored in a shared Network Attached Storage \(NAS\) volume that is declared by a PV and a PVC. In this example, a PVC that is named **training-data** is created. The PVC uses a shared PV. The datasets in this example are stored in the tf\_data directory of the share PV.

1.  Run the following command to query the available GPU resources in the cluster:

    ```
    arena top node
    ```

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    0/6 (0%)
    ```

    The output shows that three GPU-accelerated nodes can be used to run training jobs.

2.  Run the `arena submit tfjob/tf [--flag]` command to submit a standalone PyTorch training job.

    The following sample code shows how to submit a standalone PyTorch job that runs on one node with one GPU.

    ```
      arena submit pytorch \
        --name=pytorch-git \
        --gpus=1 \
        --working-dir=/root \
        --image=registry.cn-huhehaote.aliyuncs.com/lumo/pytorch-with-tensorboard:1.5.1-cuda10.1-cudnn7-runtime \
        --sync-mode=git \
        --sync-source=https://code.aliyun.com/370272561/mnist-pytorch.git \
        --data=training-data:/mnist_data \
        --tensorboard \
        --logdir=/mnist_data/pytorch_data/logs \
        "python /root/code/mnist-pytorch/mnist.py --epochs 10 --backend nccl --dir /mnist_data/pytorch_data/logs --data /mnist_data/pytorch_data/"
    ```

    ```
    configmap/pytorch-git-pytorchjob created
    configmap/pytorch-git-pytorchjob labeled
    service/pytorch-git-tensorboard created
    deployment.apps/pytorch-git-tensorboard created
    pytorchjob.kubeflow.org/pytorch-git created
    INFO[0000] The Job pytorch-git has been submitted successfully
    INFO[0000] You can run `arena get pytorch-git --type pytorchjob` to check the job status
    ```

    The following table lists the parameters in the preceding code block.

    |Parameter|Required|Description|Default|
    |---------|--------|-----------|-------|
    |--name|Yes|Specifies the name of the submitted job. The name must be globally unique.|N/A|
    |--working-dir|No|Specifies the directory where the command is executed.|/root|
    |--gpus|No|Specifies the number of GPUs that are used by the worker node where the standalone PyTorch job runs.|0|
    |--image|Yes|Specifies the address of the image that is used to deploy the runtime.|N/A|
    |--sync-mode|No|Specifies the synchronization mode. Valid values: git and rsync. The git-sync mode is used in this example.|N/A|
    |--sync-source|No|The address of the repository from which the source code is sychronized. This parameter is used in combination with the --sync-mode parameter. The git-sync mode is used in this example. Therefore, you must specify a Git repository address, such as the URL of a project on GitHub or Alibaba Cloud. The source code of the project is downloaded to the code/ directory under --working-dir. The directory is /root/code/mnist-pytorch in this example.|N/A|
    |--data|No|Mounts a shared PV to the runtime where the training job runs. The value of this parameter consists of two parts that are separated by a colon \(`:`\). Specify the name of the PVC on the right side of the colon. To query the name of the PVC, run the `arena data list` command. This command queries the PVCs that are available for the target cluster. Specify the path to which the PV claimed by the PVC is mounted on the right side of the colon. This way, your training job can retrieve the data stored in the corresponding PV claimed by the PVC.**Note:** Run the `arena data list` command to query the PVCs that are available for the target cluster.

    ```
NAME           ACCESSMODE     DESCRIPTION  OWNER  AGE
training-data  ReadWriteMany                      35m
    ```

If no PVC is available, create one. For more information, see [Configure a shared NAS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared NAS volume.md) or [Configure a shared CPFS volume](/intl.en-US/Solutions/Deep learning solution/Preparations/Configure a shared CPFS volume.md).

|N/A|
    |--tensorboard|No|Specifies that TensorBoard is used to visualize training results. You can set this parameter in combination with the --logdir parameter to specify the path from which TensorBoard reads event files. If you do not specify this parameter, TensorBoard is not used.|N/A|
    |--logdir|No|Specifies the path from which TensorBoard reads event files. It must be used in combination with --tensorboard.|/training\_logs|

    **Note:** If you use a non-public Git repository, run the following command to submit a standalone PyTorch training job:

    ```
      arena --loglevel info submit pytorch \
            ...
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/370272561/mnist-pytorch.git \
            --env=GIT_SYNC_USERNAME=yourname \
            --env=GIT_SYNC_PASSWORD=yourpwd \
            "python /root/code/mnist-pytorch/mnist.py --backend gloo"
    ```

    In the preceding code block, the Arena client synchronizes the source code in [git-sync](https://github.com/kubernetes/git-sync/blob/master/cmd/git-sync/main.go) mode. You can customize the environment variables that are defined in the corresponding code project.

3.  Run the following command to query the status of all submitted jobs:

    ```
    arena list
    ```

    ```
    NAME         STATUS     TRAINER     AGE  NODE
    pytorch-git  RUNNING    PYTORCHJOB  19s  192.1xx.x.xx
    tf-dist      SUCCEEDED  TFJOB       13h  N/A
    tf-git       SUCCEEDED  TFJOB       16h  N/A
    ```

4.  Run the following command to query the GPU resources used by these jobs:

    ```
    arena top job
    ```

    ```
    NAME         GPU(Requests)  GPU(Allocated)  STATUS     TRAINER     AGE  NODE
    tf-dist      2              0               SUCCEEDED  tfjob       13h  N/A
    tf-git       1              0               SUCCEEDED  tfjob       16h  N/A
    pytorch-git  1              1               RUNNING    pytorchjob  25s  192.1xx.x.xx
    
    
    Total Allocated GPUs of Training Job:
    1
    
    Total Requested GPUs of Training Job:
    4
    ```

5.  Run the following command to query the GPU resources in the cluster:

    ```
    arena top node
    ```

    ```
    NAME                       IPADDRESS     ROLE    STATUS  GPU(Total)  GPU(Allocated)
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  master  ready   0           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           0
    cn-huhehaote.192.1xx.x.xx  192.1xx.x.xx  <none>  ready   2           1
    -----------------------------------------------------------------------------------------
    Allocated/Total GPUs In Cluster:
    1/6 (16%)
    ```

6.  Run the following command to query detailed information about a job:

    ```
    arena get pytorch-git
    ```

    ```
    STATUS: SUCCEEDED
    NAMESPACE: default
    PRIORITY: N/A
    TRAINING DURATION: 2m
    
    NAME         STATUS     TRAINER     AGE  INSTANCE              NODE
    pytorch-git  SUCCEEDED  PYTORCHJOB  3m   pytorch-git-master-0  192.16x.x.xx
    
    Your tensorboard will be available on:
    http://192.16x.x.xx:30171
    ```

    **Note:** TensorBoard is used in this example. Therefore, you can find the URL of TensorBoard in the last two rows of the job information. If TensorBoard is not used, the last two rows are not returned.

7.  Use a browser to view the training results in TensorBoard.

    In the preceding section, you can obtain the URL of TensorBoard from the job information. The ACK cluster is deployed in another region. Therefore, you must use sshuttle to view the visualized training results in your browser.

    The following sample code shows how to use sshuttle as a proxy:

    ```
    # you can install sshuttle==0.74 in your mac with python2.7
    pip install sshuttle==0.74
    # 0/0 -> 0.0.0.0/0
    sshuttle -r root@39.104.xx.xxx  0/0
    ```

    **Note:** 39.104.xx.xxx is the public IP address of the ACK cluster. In addition, you must check whether port 22 is opened in your security group. By default, port 22 is opened.

    Copy the URL \(http://192.16x.x.xx:30171\) that is obtained in Step 6 to the address bar of your browser and press Enter. The TensorBoard page appears.

    ![Standalone PyTorch jobs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4186258951/p135067.png)

    **Note:** The source code that is used to submit the standalone PyTorch job in this topic indicates that training results are written into events after every 10 epochs. If you want to modify the value of --epochs, set the value to a multiple of 10. Otherwise, the training results cannot be visualized in TensorBoard.

8.  Run the following command to print the job logs:

    ```
    arena logs pytorch-git
    ```

    ```
    WORLD_SIZE: 1, CURRENT_RANK: 0
    args: Namespace(backend='nccl', batch_size=64, data='/mnist_data/data', dir='/mnist_data/logs', epochs=1, log_interval=10, lr=0.01, momentum=0.5, no_cuda=False, save_model=False, seed=1, test_batch_size=1000)
    Using CUDA
    ...
    Train Epoch: 10 [58240/60000 (97%)] loss=0.0128
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0098
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```

    You can run the `arena logs $job_name -f` command to print the job logs in real time and run the `arena logs $job_name -t N` command to print N lines from the bottom of the log. You can also run the `arena logs --help` command to query parameters for printing logs.

    The following sample code shows how to print N lines from the bottom of the log.

    ```
    arena logs pytorch-git -t 5
    ```

    ```
    Train Epoch: 10 [58880/60000 (98%)] loss=0.0098
    Train Epoch: 10 [59520/60000 (99%)] loss=0.0051
    
    accuracy=0.9904
    ```


