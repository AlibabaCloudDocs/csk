---
keyword: GPU-accelerated elastic container instances
---

# Use GPU-accelerated elastic container instances

In this topic, a TensorFlow job for image recognition is used as an example in an ASK cluster to demonstrate how to use GPU-accelerated elastic container instances.

A serverless Kubernetes \(ASK\) cluster is created or a virtual node is created in a Container Service for Kubernetes \(ACK\) cluster. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md) or [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

ASK supports GPU-accelerated elastic container instances. This allows you to run Artificial Intelligence \(AI\) computing tasks in a serverless framework. Using GPU-accelerated elastic container instances greatly reduces the loads of resource maintenance on the AI computing platform and improves the computing efficiency.

GPU acceleration is essential to AI computing. However, creating a cluster with GPU resources is a complex task that involves purchasing GPUs, preparing machines, installing drivers, and deploying container runtimes. The serverless delivery mode of GPU resources demonstrates the key benefits of serverless. This delivery mode provides standardized and out-of-the-box resource supply capabilities. Users do not need to purchase machines or log on to nodes to install GPU drivers. This simplifies the deployment of the AI computing platform and allows you to focus on the development and maintenance of AI computing models and applications instead of the infrastructure. GPU and CPU resources are ready to use and easy to obtain. Compared with the subscription billing method, the pay-as-you-go billing method reduces both the costs and waste of resources.

When you create pods that are mounted with GPU resources in an ASK cluster, you can set an annotation to specify the GPU model. In addition, you can specify the number of GPUs or instance-type in `resource.limits`. Each GPU is exclusive to a pod. The costs of GPU-accelerated elastic container instances are the same as those of GPU-accelerated Elastic Compute Services \(ECS\) instances. No additional fees are charged.

**Note:** Pods that are mounted with vGPU resources are not supported.

## Procedure

A TensorFlow job for image recognition is used as an example in the ASK cluster to demonstrate how to use GPU-accelerated elastic container instances.

![image](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5397297951/p47460.png)

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, click **Create from YAML**, select a sample template or customize a template, and then click **Create**.

    You can use the following YAML template to create a pod. In this example, the specified GPU model in the pod is P4 and the number of GPUs is 1.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: tensorflow
      annotations:
        k8s.aliyun.com/eci-gpu-type : "P4"
    spec:
      containers:
      - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
        name: tensorflow
        command:
        - "sh"
        - "-c"
        - "python models/tutorials/image/imagenet/classify_image.py"
        resources:
          limits:
            nvidia.com/gpu: "1"
      restartPolicy: OnFailure
    ```

6.  In the left-side navigation pane, choose **Workloads** \> **Pods** to view the states of pods.

7.  Click the pod that you want to view. On the Pods - tensorflow page, click the **Logs** tab. If the content in the following red box appears, the image is recognized.

    ![Pods - tensorflow](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5397297951/p47463.png)


For more information about how to use this feature on a virtual node in an ACK cluster, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md). You need to schedule the pod to the virtual node, or create the pod in a namespace that contains the virtual-node-affinity-injection=enabled label. Then, use the following file to replace the YAML file in Step [5](#step_a0i_3au_ds9).

```
apiVersion: v1
kind: Pod
metadata:
  name: tensorflow
  annotations:
    k8s.aliyun.com/eci-gpu-type : "P4"
spec:
  containers:
  - image: registry-vpc.cn-hangzhou.aliyuncs.com/ack-serverless/tensorflow
    name: tensorflow
    command:
    - "sh"
    - "-c"
    - "python models/tutorials/image/imagenet/classify_image.py"
    resources:
      limits:
        nvidia.com/gpu: "1"
  restartPolicy: OnFailure
  nodeName: virtual-kubelet
```

**Note:** More deep learning frameworks are supported if you use virtual nodes. For example, you can use Kubeflow, arena, and other custom resource definitions \(CRDs\).

