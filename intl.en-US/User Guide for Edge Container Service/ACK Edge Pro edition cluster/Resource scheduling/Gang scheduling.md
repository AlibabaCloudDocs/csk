---
keyword: [gang scheduling, resources scheduling, coordinated scheduling]
---

# Gang scheduling

Container Service for Kubernetes \(ACK\) provides the gang scheduling feature based on the new kube-scheduler framework. This feature provides a solution to job scheduling in the all-or-nothing scenario. This topic describes how to enable gang scheduling.

-   A professional managed Kubernetes cluster is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/ACK Pro clusters/Create a professional managed Kubernetes cluster.md).

    **Note:** Gang scheduling is available for only professional managed Kubernetes clusters. To enable gang scheduling for dedicated Kubernetes clusters, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to add your account to the whitelist.

-   The following table describes the system component versions that are required for topology-aware CPU scheduling.

    |Component|Required version|
    |---------|----------------|
    |Kubernetes|V1.16 and later|
    |Helm|V3.0 and later|
    |Docker|19.03.5|
    |Operating system|CentOS 7.6, CentOS 7.7, Ubuntu 16.04, Ubuntu 18.04, and Alibaba Cloud Linux 2.|


Gang scheduling is a scheduling algorithm that schedules all correlated processes to different processors in a parallel system and starts these processes simultaneously. Gang scheduling aims to start all correlated processes at the same time. This prevents the process group from being blocked when the system fails to start some processes. For example, if you submit a batch job that contains multiple tasks, either all of the tasks are scheduled or none of them is scheduled. Task scheduling in the all-or-nothing scenario is known as gang scheduling.

Kubernetes is widely used in online service orchestration. ACK wants to use Kubernetes as a platform for unified management of online services and offline jobs. This improves the resource utilization and performance of clusters. However, kube-scheduler cannot migrate specific offline workloads to Kubernetes clusters. For example, if a job requires all-or-nothing scheduling, all tasks of the job must be scheduled at the same time. If only some of the tasks are started, the started jobs must wait until all the remaining tasks are scheduled. If each submitted job contains unscheduled tasks, all submitted jobs remain in the Pending state and the cluster is deadlocked. To avoid this situation, you must enable gang scheduling for kube-scheduler.

## Features

In ACK, a pod group is a group of pods that need to be scheduled at the same time. When you submit a job that requires all-or-nothing scheduling, you can add labels to pods. The labels specify the name of the pod group to which the job belongs and the minimum number of tasks that must be scheduled to run the job. kube-scheduler schedules tasks based on the minimum number of tasks that must be scheduled. The tasks are scheduled only when the cluster resources are sufficient to schedule the required number of tasks. Otherwise, the job remains in the Pending state.

## How to enable gang scheduling

To enable gang scheduling, set min-available and name by adding labels to the pods.

```
labels:
    pod-group.scheduling.sigs.k8s.io/name: tf-smoke-gpu
    pod-group.scheduling.sigs.k8s.io/min-available: "3"
```

-   name: Specifies the name of a pod group.
-   min-available: Specifies the minimum number of pods that must be scheduled to run a job. Pods are scheduled only when the computing resources are sufficient to schedule the required number of pods.

**Note:** Pods in the same pod group must be assigned the same priority.

## Examples

In this example, a distributed TensorFlow job is used to demonstrate how to enable gang scheduling. The ACK cluster that is used in this example has four GPUs.

1.  Run the following command to use [Arena](https://github.com/kubeflow/arena) of Kubeflow to deploy the environment in your ACK cluster to run the distributed TensorFlow job.

    **Note:** Arena is a subproject of [Kubeflow](https://github.com/kubeflow). Kubeflow is an open source project for machine learning based on Kubernetes. Arena allows you to manage the lifecycle of machine learning jobs by using the command line interface or SDK. Lifecycle management includes environment setup, data preparation, model development, model training, and model prediction. This improves the working efficiency of data scientists.

    ```
    git clone https://github.com/kubeflow/arena.git
    kubectl create ns arena-system
    kubectl create -f arena/kubernetes-artifacts/jobmon/jobmon-role.yaml
    kubectl create -f arena/kubernetes-artifacts/tf-operator/tf-crd.yaml
    kubectl create -f arena/kubernetes-artifacts/tf-operator/tf-operator.yaml
    ```

    Run the following command to check whether the environment to run TensorFlow jobs is deployed. If the pods are in the Running state, it indicates that the environment is deployed.

    ```
    kubectl  get pods -n arena-system
    ```

    ```
    NAME                                READY   STATUS    RESTARTS   AGE
    tf-job-dashboard-56cf48874f-gwlhv   1/1     Running   0          54s
    tf-job-operator-66494d88fd-snm9m    1/1     Running   0          54s
    ```

2.  Use the following template to submit a distributed TensorFlow job to the ACK cluster. The job runs on one parameter server \(PS\) pod and four worker pods. Each worker pod requires two GPUs.

    ```
    apiVersion: "kubeflow.org/v1"
    kind: "TFJob"
    metadata:
      name: "tf-smoke-gpu"
    spec:
      tfReplicaSpecs:
        PS:
          replicas: 1
          template:
            metadata:
              creationTimestamp: null
              labels:
                pod-group.scheduling.sigs.k8s.io/name: tf-smoke-gpu
                pod-group.scheduling.sigs.k8s.io/min-available: "5"
            spec:
              containers:
              - args:
                - python
                - tf_cnn_benchmarks.py
                - --batch_size=32
                - --model=resnet50
                - --variable_update=parameter_server
                - --flush_stdout=true
                - --num_gpus=1
                - --local_parameter_device=cpu
                - --device=cpu
                - --data_format=NHWC
                image: registry.cn-hangzhou.aliyuncs.com/kubeflow-images-public/tf-benchmarks-cpu:v20171202-bdab599-dirty-284af3
                name: tensorflow
                ports:
                - containerPort: 2222
                  name: tfjob-port
                resources:
                  limits:
                    cpu: '1'
                workingDir: /opt/tf-benchmarks/scripts/tf_cnn_benchmarks
              restartPolicy: OnFailure
        Worker:
          replicas: 4
          template:
            metadata:
              creationTimestamp: null
              labels:
                pod-group.scheduling.sigs.k8s.io/name: tf-smoke-gpu
                pod-group.scheduling.sigs.k8s.io/min-available: "5"
            spec:
              containers:
              - args:
                - python
                - tf_cnn_benchmarks.py
                - --batch_size=32
                - --model=resnet50
                - --variable_update=parameter_server
                - --flush_stdout=true
                - --num_gpus=1
                - --local_parameter_device=cpu
                - --device=gpu
                - --data_format=NHWC
                image: registry.cn-hangzhou.aliyuncs.com/kubeflow-images-public/tf-benchmarks-gpu:v20171202-bdab599-dirty-284af3
                name: tensorflow
                ports:
                - containerPort: 2222
                  name: tfjob-port
                resources:
                  limits:
                    nvidia.com/gpu: 2
                workingDir: /opt/tf-benchmarks/scripts/tf_cnn_benchmarks
              restartPolicy: OnFailure
    ```

    -   Submit the distributed TensorFlow job without enabling gang scheduling

        Run the following command to query the states of pods. Only two worker pods are running and the other worker pods are in the Pending state.

        ```
        kubectl get pods
        ```

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        tf-smoke-gpu-ps-0       1/1     Running   0          6m43s
        tf-smoke-gpu-worker-0   1/1     Running   0          6m43s
        tf-smoke-gpu-worker-1   1/1     Running   0          6m43s
        tf-smoke-gpu-worker-2   0/1     Pending   0          6m43s
        tf-smoke-gpu-worker-3   0/1     Pending   0          6m43s
        ```

        Run the following command to query log data of the running worker pods. The returned log data indicates that the running worker pods are waiting for the system to start the pending worker pods. The GPU resources occupied by the running worker pods are not in use.

        ```
        kubectl logs -f tf-smoke-gpu-worker-0
        ```

        ```
        INFO|2020-05-19T07:02:18|/opt/launcher.py|27| 2020-05-19 07:02:18.199696: I tensorflow/core/distributed_runtime/master.cc:221] CreateSession still waiting for response from worker: /job:worker/replica:0/task:3
        INFO|2020-05-19T07:02:28|/opt/launcher.py|27| 2020-05-19 07:02:28.199798: I tensorflow/core/distributed_runtime/master.cc:221] CreateSession still waiting for response from worker: /job:worker/replica:0/task:2
        ```

    -   Submit the distributed TensorFlow job with gang scheduling enabled

        Run the following command to query the states of pods. The computing resources in the cluster are insufficient to schedule the minimum number of pods. Therefore, the pod group cannot be scheduled and all pods are in the Pending state.

        ```
        kubectl get pods
        ```

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        tf-smoke-gpu-ps-0       0/1     Pending   0          43s
        tf-smoke-gpu-worker-0   0/1     Pending   0          43s
        tf-smoke-gpu-worker-1   0/1     Pending   0          43s
        tf-smoke-gpu-worker-2   0/1     Pending   0          43s
        tf-smoke-gpu-worker-3   0/1     Pending   0          43s
        ```

        After four GPUs are allocated to the cluster, the computing resources in the cluster are sufficient to schedule the minimum number of pods. After the pod group is scheduled, the four worker pods start to run. Run the following command to query the states of pods:

        ```
        kubectl get pods
        ```

        ```
        NAME                    READY   STATUS    RESTARTS   AGE
        tf-smoke-gpu-ps-0       1/1     Running   0          3m16s
        tf-smoke-gpu-worker-0   1/1     Running   0          3m16s
        tf-smoke-gpu-worker-1   1/1     Running   0          3m16s
        tf-smoke-gpu-worker-2   1/1     Running   0          3m16s
        tf-smoke-gpu-worker-3   1/1     Running   0          3m16s
        ```

        Run the following command to query log data of a running worker pod. The following output indicates that the tasks have been started.

        ```
        kubectl logs -f tf-smoke-gpu-worker-0
        ```

        ```
        INFO|2020-05-19T07:15:24|/opt/launcher.py|27| Running warm up
        INFO|2020-05-19T07:21:04|/opt/launcher.py|27| Done warm up
        INFO|2020-05-19T07:21:04|/opt/launcher.py|27| Step  Img/sec loss
        INFO|2020-05-19T07:21:05|/opt/launcher.py|27| 1 images/sec: 31.6 +/- 0.0 (jitter = 0.0) 8.318
        INFO|2020-05-19T07:21:15|/opt/launcher.py|27| 10  images/sec: 31.1 +/- 0.4 (jitter = 0.7) 8.343
        INFO|2020-05-19T07:21:25|/opt/launcher.py|27| 20  images/sec: 31.5 +/- 0.3 (jitter = 0.7) 8.142
        ```


