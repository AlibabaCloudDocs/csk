# Container configurations and labels for creating an application from an image

This topic describes the differences in container configurations and labels when you create an application from an image in a cluster of Container Service for Swarm and in a cluster of Container Service for Kubernetes \(ACK\).

## Create an application from an image

The interfaces for creating an application from an image in a Swarm cluster and in an ACK cluster are quite different.

-   For more information about how to create an application from an image in a Swarm cluster, see [Create an application](/intl.en-US/User Guide/Applications/Create an application.md).
-   For more information about how to create an application from an image in an ACK cluster, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

## Container configurations

Swarm cluster

To configure a container, you must set start commands \(**Command** and **Entrypoint**\), resource limits \(**CPU** and **Memory**\), and start parameters.

![Swarm cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7546858951/p35453.png)

ACK cluster

The container configurations in Swarm clusters are similar to the basic configurations and lifecycle configurations of containers in an ACK cluster.

-   For more information about the lifecycle configurations of a container, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    -   **Start**
    -   **Post Start**
    -   **Pre Stop**
    ![ACK cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7546858951/p35526.png)

-   For more information about how to configure the basic settings of a container in an ACK cluster, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md). For the recommended settings, see [Recommended Kubernetes cluster configurations to run highly reliable applications](/intl.en-US/Best Practices/Cluster/ECS instance selection and cluster configurations/Recommended Kubernetes cluster configurations to run highly reliable applications.md).
    -   **Resource Limit**
    -   **Required Resources**

## Label

In Swarm clusters, labels can be used to implement health checks, domain name settings, and logging.

In ACK clusters, labels are only used to identify applications. When you create an application in an ACK cluster, a label named after the application name is automatically generated. This label is not displayed when you create the application from an image. You can configure YAML files to use the label.

