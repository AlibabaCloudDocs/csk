# Use a StatefulSet to create a stateful application

Container Service for Kubernetes \(ACK\) allows you to create stateful applications by using the ACK console. In this topic, a stateful NGINX application is created as an example. The features of StatefulSets are also demonstrated.

Before you deploy a stateful application from an image, make sure that you have performed the following steps:

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Create a PVC](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Create a PVC.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

StatefulSets provide the following features:

|Feature|Description|
|-------|-----------|
|Pod consistency|Pod consistency guarantees that pods are started and terminated in the specified order and also guarantees the consistency of networks. The consistency of pods is determined by the configurations of the pods, regardless of the node to which a pod is scheduled.|
|Stable and persistent storage|VolumeClaimTemplate allows you to mount a persistent volume \(PV\) to each pod. The mounted PVs are not deleted after you delete or scale in the pod replicas.|
|Stable network identifiers|Each pod in a StatefulSet derives its hostname from the name of the StatefulSet and the ordinal of the pod. The pattern of the hostname is `StatefulSet name-pod ordinal`.|
|Stable orders|For a StatefulSet with N pod replicas, each pod is assigned an integer ordinal from 0 to N-1. The ordinals assigned to pods within the StatefulSet are unique.|

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **StatefulSets**.

5.  In the upper-right corner of the **StatefulSets** page, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure the basic settings.

    In this example, the Type parameter is set to **StatefulSets** to deploy a stateful application.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace where you want to deploy the application. The default namespace is automatically selected. You can select another namespace.|
    |Replicas|The number of pods that are provisioned for the application.|
    |Type|The type of application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, or **DaemonSets**.|
    |Label|Add a label to the application. The label is used to identify the application.|
    |Annotations|Add an annotation to the application.|
    |Synchronize Timezone|Specify whether to synchronize the time zone between nodes and containers.|

7.  Click **Next** to proceed to the **Container** wizard page.

8.  Configure containers.

    **Note:** In the upper part of the **Container** wizard page, click **Add Container** to add more containers for the application.

    The following table describes the parameters that are required to configure the containers.

    -   General settings

        |Parameter|Description|
        |---------|-----------|
        |Image Name|        -   To use a Docker image or an image from Container Registry, click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, an NGINX image is selected. On the **Search** tab, select **Docker Images** from the drop-down list, enter NGINX in the search bar, and then click Search.
            -   Alibaba Cloud Container Registry: On the **Alibaba Cloud Container Registry** tab, you can select an image from Container Registry. Specify the region and the Container Registry instance to which the image belongs. For more information about Container Registry, see [What is Container Registry Enterprise Edition?]().

**Note:** On the **Alibaba Cloud Container Registry** tab, you can enter an image name to search for an image.

            -   Docker Official Images: On the **Docker Official Images** tab, you can select a Docker image.
            -   Favorite Images: On the **Favorite Images** tab, you can select a Docker image that you have added to your favorite list.
            -   Search: On the **Search** tab, you can select Alibaba Cloud Image from the drop-down list and specify a region to search for an image in Container Registry. You can also select Docker Images from the drop-down list and search for a Docker image.
        -   You can also enter the address of a private registry. The registry address must be in the `domainname/namespace/imagename:tag` format. |
        |Image Version

|        -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
        -   You can select the following image pull policies:

            -   **ifNotPresent**: If the image you want to pull is found in the region where the cluster is deployed, the local image is used. Otherwise, ACK pulls the image from the corresponding repository.
            -   **Always**: ACK pulls the image from the repository each time the application is deployed or expanded.
            -   **Never**: ACK uses only local images.
**Note:** If you select **Image Pull Policy**, no image pull policy is applied for the deployment of the application.

        -   To pull the image without a secret, click **Set Image Pull Secret** to set a secret for pulling images. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use aliyun-acr-credential-helper to pull images without a password.md). |
        |Resource Limit|You can specify an upper limit for the CPU, memory, and ephemeral storage space that the container can consume. This prevents the container from occupying an excess amount of resources. The CPU resource is measured in millicores \(one thousandth of one core\). The memory resource is measured in MiB. The ephemeral storage resource is measured in GiB.|
        |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes occupy these resources.|
        |Container Start Parameter|        -   stdin: specifies that start parameters defined in the console are sent to the container.
        -   tty: specifies that start parameters defined in a virtual terminal are sent to the console. |
        |Privileged Container|        -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
        -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
        |Init Container|If you select Init Container, an init container is created. An init container contains useful utilities. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

    -   Ports

        Configure container ports.

        -   Name: Enter a name for the port.
        -   Container Port: Enter the container port that you want to open. Enter a port number from 1 to 65535.
        -   Protocol: Select TCP or UDP.
    -   Environments

        You can configure environment variables in key-value pairs for pods. Environment variables are used to apply pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

        -   Type: Select the type of environment variable. You can select **Custom**, **ConfigMaps**, **Secret**, or **Value/ValueFrom**. If you select ConfigMaps or Secret as the type of environment variable, all values in the selected ConfigMap or Secret are passed to the container environment variables. In this example, Secret is selected.

            Select **Secret** from the Type drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All values in the selected Secret are passed to the environment variable.

            ![Environment variable](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130410.png)

            In this case, the YAML file used to deploy the application contains the settings that reference all values in the specified Secret.

            ![yaml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130413.jpg)

        -   Variable Key: Specify the key of the environment variable.
        -   Value/ValueFrom: Specify the value that is referenced by the environment variable.
    -   Health Check

        Health check settings include liveness and readiness probes. Liveness probes determine when to restart the container. Readiness probes determine whether the container is ready to accept network traffic. For more information about health checks, see [Configure Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

        |Request type|Description|
        |------------|-----------|
        |HTTP request|Sends an HTTP GET request to the container. You can configure the following parameters:        -   Protocol: HTTP or HTTPS.
        -   Path: the requested path on the server.
        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 3.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successes that must occur before a container is considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failures that must occur before a container is considered unhealthy after a success. Default value: 3. Minimum value: 1. |
        |TCP|Sends a TCP socket to the container. Kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can configure the following parameters:        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successes that must occur before a container is considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failures that must occur before a container is considered unhealthy after a success. Default value: 3. Minimum value: 1. |
        |Command|Runs a probe command in the container to check the health status of the container. You can configure the following parameters:        -   Command: Enter the probe command that is run to check the health status of the container.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 5.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probles are performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successes that must occur before a container is considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failures that must occur before a container is considered unhealthy after a success. Default value: 3. Minimum value: 1. |

    -   Lifecycle

        You can configure the lifecycle of the container by using the following parameters: Start, Post Start, and Pre Stop. For more information, see [Attach Handlers to Container Lifecycle Events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: Set the command and parameter that take effect before the container starts.
        -   **Post Start**: Set the command that takes effect after the container starts.
        -   **Pre Stop**: Set the command that takes effect before the container stops.
        ![Lifecycle](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6136769161/p134222.png)

    -   Volume

        You can mount on-premises volumes and persistent volume claims \(PVCs\) to the container.

        -   **Add Local Storage**: You can select HostPath, ConfigMap, Secret, and EmptyDir. The specified volume is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Add PVC**: You can select Cloud Storage.
        In this example, a PVC named disk-ssd is mounted to the /tmp path of the container.

        ![Configure a volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

    -   Log

        Configure **Log Service**. You can specify collection configurations and add custom tags.

        **Note:** Make sure that the Log Service agent is installed in the cluster.

        |Parameter|Description|
        |---------|-----------|
        |Collection Configuration|Logstore: Create a Logstore in Log Service to store log data.|
        |Log Path in Container: Specify stdout or a path to collect log data

        -   stdout: specifies that the stdout files are collected.
        -   Text Logs: specifies that logs in the specified path of the container are collected. In this example, /var/log/nginx is specified as the path. Wildcard characters can be used to specify the path. |
        |Custom Tag|You can also add custom tags. Custom tags are added to the logs of the container when the logs are collected. Log data with tags is easier to aggregate and filter.|

9.  Configure the parameters based on your business requirements and click **Next**.

10. Configure advanced settings.

    -   Access Control

        **Note:**

        You can configure the following access control settings based on your business requirements:

        -   Internal applications: For applications that run inside the cluster, you can create a ClusterIP or NodePort Service to enable internal communication.
        -   External applications: For applications that are exposed to the Internet, you can configure access control by using one of the following methods:
            -   Create a LoadBalancer Service and enable access to your application over the Internet by using a Server Load Balancer \(SLB\) instance.
            -   Create an Ingress and use the Ingress to expose your application to the Internet. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
        You can also specify how the backend pods are exposed to the Internet. In this example, a ClusterIP Service and an Ingress are created to expose the NGINX application to the Internet.

        |Parameter|Description|
        |---------|-----------|
        |Service|Click **Create** on the right side of **Services**. In the **Create Service** dialog box, configure the parameters. For more information about the parameters, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md). **Cluster IP** is selected in this example.|
        |Ingress|Click **Create** on the right side of **Ingresses**. In the Create dialog box, configure the parameters. For more information about how to configure an Ingress, see [Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md). **Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, the name of a virtual host is used as the test domain name. You must add the following entry to the hosts file to map the domain name to the IP address of the Ingress. In actual scenarios, use a domain name that has obtained an Internet Content Provider \(ICP\) number.

        ```
101.37.224.146   foo.bar.com    #The IP address of the Ingress.
        ``` |

        You can find the created Service and Ingress in the **Access Control** section. You can click **Update** or **Delete** to change the configurations.

    -   Scaling

        In the **Scaling** section, specify whether to enable **HPA** and **CronHPA**. This allows you to meet the resource requirements of the application at different load levels.

        ![Scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4104519061/p10978.png)

        -   HPA can automatically scale the number of pods in an ACK cluster based on the CPU and memory usage.

            **Note:** To enable HPA, you must configure required resources for the container. Otherwise, HPA does not take effect.

            |Parameter|Description|
            |---------|-----------|
            |Metric|Select CPU Usage or Memory Usage. The selected resource type must be the same as that specified in the Required Resources field.|
            |Condition|Specify the resource usage threshold. HPA triggers scaling activities when the threshold is exceeded.|
            |Max. Replicas|Specify the maximum number of pod replicas to which the application can be scaled.|
            |Min. Replicas|Specify the minimum number of pod replicas that must run.|

        -   CronHPA can scale an ACK cluster at a scheduled time. For more information about CronHPA, see [Create CronHPA jobs](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).
    -   Scheduling

        You can set the following parameters: Update Method, Node Affinity, Pod Affinity, Pod Anti Affinity, and Toleration. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity).

        **Note:** Node affinity and pod affinity affect pod scheduling based on node labels and pod labels. You can add node labels and pod labels that are provided by Kubernetes to configure node affinity and pod affinity. You can also add custom labels to nodes and pods, and then configure node affinity and pod affinity based on these custom labels.

        |Parameter|Description|
        |---------|-----------|
        |Update Method|Select Rolling Update or OnDelete. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx). |
        |Node Affinity|Add labels to worker nodes to set **Node Affinity**. Node Affinity supports required and preferred rules, and various operators, such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

        -   **Required**: Specify the rules that must be matched for a pod to be scheduled to a node. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. These rules have the same effect as the `NodeSelector` parameter. In this example, pods can be scheduled to only nodes with the specified labels. You can create multiple required rules. However, only one of them must be met.
        -   **Preferred**: Specify the rules that are not required to be matched for pod scheduling. Pods are scheduled to a node that matches the preferred rules when multiple nodes match the required rules. In the YAML file, these rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. In this example, the scheduler attempts to schedule a pod to a node that matches the preferred rules. You can set node weights in preferred rules. If multiple nodes match the required and preferred rules, the node with the highest weight is preferred for pod scheduling. You can create multiple preferred rules. However, all of them must be met before the pod can be scheduled. |
        |Pod Affinity|Pod affinity specifies that pods can be scheduled to nodes or topological domains where pods with matching labels are deployed. For example, you can use pod affinity to deploy services that communicate with each other to the same topological domain, such as a host. This reduces the network latency between these services. Pod affinity enables you to specify to which node pods can be scheduled based on the labels on other running pods. Pod affinity supports required and preferred rules, and the following operators: `In, NotIn, Exists, and DoesNotExist`.

        -   **Required**: Specify rules that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. A node must match the required rules before pods can be scheduled to the node.
            -   **Namespace**: Specify the namespace to apply the required rule. Pod affinity rules are defined based on the labels that are added to pods and therefore must be scoped to a namespace.
            -   **Topological Domain**: Set the topologyKey. This specifies the key for the node label that the system uses to denote the topological domain. For example, if you set the parameter to `kubernetes.io/hostname`, topologies are determined by nodes. If you set the parameter to `beta.kubernetes.io/os`, topologies are determined by the operating systems of nodes.
            -   **Selector**: Click Add to add pod labels.
            -   **View Applications**: Click **View Applications** and set the namespace and application in the dialog box that appears. You can view the pod labels on the selected application and add the labels as selectors.
            -   Required Rules: Specify labels on existing applications, the operator, and the label value. In this example, the required rule specifies that the application to be created is scheduled to a host that runs applications with the `app:nginx` label.
        -   **Preferred**: Specify rules that are not required to be matched for pod scheduling. In the YAML file, preferred rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. The scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. Configure the other parameters as described in the preceding settings.

**Note:** **Weight**: Set the weight of a preferred rule to a value from 1 to 100. The scheduler calculates the weight of each node that meets the preferred rule based on an algorithm, and then schedules the pod to the node with the highest weight. |
        |Pod Anti Affinity|Pod anti-affinity rules specify that pods are not scheduled to topological domains where pods with matching labels are deployed. Pod anti-affinity rules apply to the following scenarios:

        -   Schedule the pods of an application to different topological domains, such as multiple hosts. This allows you to enhance the stability of the service.
        -   Grant a pod exclusive access to a node. This enables resource isolation and ensures that no other pod can share the resources of the specified node.
        -   Schedule pods of an application to different hosts if the pods may interfere with each other.
**Note:** The parameters of pod anti-affinity rules are the same as those of pod affinity rules. You can create the rules for different scenarios. |
        |Toleration|Configure toleration rules to allow pods to be scheduled to nodes with matching taints.|
        |Schedule to Virtual Nodes|Specify whether to schedule pods to virtual nodes. This option is unavailable if the cluster does not contain a virtual node.|

    -   Labels and Annotations
        -   Pod Labels: Add a label to the pod. The label is used to identify the application.
        -   Pod Annotations: Add an annotation to the pod.
11. Click **Create**.

12. After the application is created, you are redirected to the Complete wizard page. You can find the resource objects under the application and click **View Details** to view application details.

    The details page of the created stateful application appears.

13. In the upper-left corner of the page, click the **Back** icon to go to the StatefulSets page. On the StatefulSets page, you can view the created application.

14. Click **Scale** in the Actions column to scale the application.

    1.  In the Scale dialog box, set Desired Number of Pods to 3 and click OK. After you scale out the application, all pods in the application are listed in ascending order of ordinal indexes. If you scale in the application, pods are deleted in descending order of ordinal indexes. This ensures that all pods follow a specific order.

        ![Scale the application](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9045359951/p12438.png)

    2.  In the left-side navigation pane, choose **Volumes** \> **Persistent Volume Claims**. Verify that after you scale out the application, new PVs and PVCs are created for the newly added pods. However, if the application is scaled in, existing PVs and PVCs are not deleted.


## Related operations

In the left-side navigation pane, click **Clusters**. On the Clusters page, click the name of the cluster where the application is deployed or click **Applications** in the **Actions** column. In the left-side navigation pane, click **Workloads** and **StatefulSets**. On the StatefulSets page, click the name of the application that you want to manage or click **Details** in the **Actions** column. On the details page of the application, you can **edit**, **scale**, **redeploy**, and **refresh** the application. You can also **view the YAML file** of the application.

-   Edit: On the details page of the application, click **Edit** in the upper-right corner of the page to modify the configurations of the application.
-   Scale: On the details page of the application, click **Scale** in the upper-right corner of the page to scale the application to a required number of pods.
-   View in YAML: On the details page of the application, click **View in YAML** in the upper-right corner of the page. You can **Update** and **Download** the YAML file. You can also click **Save As** to save the YAML file as a different name.
-   Redeploy: On the details page of the application, click **Redeploy** in the upper-right corner of the page to redeploy the application.
-   Refresh: On the details page of the application, click **Refresh** in the upper-right corner of the page to refresh the application details page.

Log on to a master node and run the following commands to test persistent storage.

1.  Run the following commands to create a temporary file in the cloud disk that is mounted to pod nginx-1:

    ```
    kubectl exec nginx-1 ls /tmp            #Query files in the /tmp directory.
    
    kubectl exec nginx-1 touch /tmp/statefulset         #Create a file named statefulset.
    
    kubectl exec nginx-1 ls /tmp
    lost+found
    statefulset
    ```

2.  Run the command to delete pod nginx-1 and verify data persistence:

    ```
    kubectl delete pod nginx-1
    pod"nginx-1" deleted
    ```

3.  After the system recreates and starts pod nginx-1, query the files in the /tmp directory. The following result shows that the statefulset file still exists. This indicates the high availability of the application that runs on the StatefulSet.

    ```
    kubectl exec nginx-1 ls /tmp                         #Query files in the /tmp directory.
    statefulset
    ```


