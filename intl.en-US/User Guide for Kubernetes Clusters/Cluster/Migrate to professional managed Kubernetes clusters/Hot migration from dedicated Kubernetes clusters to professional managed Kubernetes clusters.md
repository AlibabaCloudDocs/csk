---
keyword: [dedicated Kubernetes cluster, Kubernetes cluster migration, professional managed Kubernetes cluster]
---

# Hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters

Container Service for Kubernetes \(ACK\) supports hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters. You can dynamically migrate from existing dedicated Kubernetes clusters to professional managed Kubernetes clusters. This way, you can benefit from the features that are provided by professional managed Kubernetes clusters. This topic describes how to migrate from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster.

Before you can migrate from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster, an Object Storage Service \(OSS\) bucket must be created. For more information about how to create an OSS bucket, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).

**Note:** To migrate from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster, you must first [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this feature to be enabled on your account.

## Precaution

-   Make sure that the Kubernetes version of your dedicated Kubernetes cluster is V1.16 or later. Otherwise, you must upgrade the Kubernetes version. For more information about how to upgrade the Kubernetes version, see [Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md).
-   Make sure that the following pods are migrated to worker nodes: pods of control plane components, such as the API server, Kube Controller Manager, the cloud controller manager, and kube-scheduler, and pods in the kube-system namespace, excluding DaemonSet pods. Control plane components are replaced by managed components after the migration.
-   The master nodes no longer belong to the cluster and change to the **Unknown** state after the migration is completed.
-   You cannot roll back the migration after the migration is completed.
-   The Elastic Compute Service \(ECS\) instances in the dedicated Kubernetes cluster are not automatically deleted after the migration is completed. You must manually delete the ECS instances.

## Hot migration from dedicated Kubernetes clusters to professional managed Kubernetes clusters

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the dedicated Kubernetes cluster that you want to migrate and click **Migrate to Professional Managed Kubernetes** in the **Actions** column.

4.  In the **Migrate to Professional Managed Kubernetes** dialog box, grant the permissions that are required to perform the migration and then click **OK**.

    To migrate from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster, you must perform the following operations to grant the required permissions.

    1.  In the **Migrate to Professional Managed Kubernetes** dialog box, click **RAM console**.

        ![migrate](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6261403261/p269274.png)

    2.  On the details page of the master Resource Access Management \(RAM\) role, click the policy whose name starts with **k8sMasterRolePolicy**.

        ![master role](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8260651261/p263338.png)

    3.  On the details page of this policy, click **Modify Policy Document** on the **Policy Document** tab.

    4.  In the **Modify Policy Document** panel, add the following content to the **Statement** section and then click **OK**.

        ```
        ,
                {
                    "Action": [
                        "oss:PutObject",
                        "oss:GetObject"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "acs:oss:*:*:<YOUR_BUCKET_NAME>/*"  # Replace <YOUR_BUCKET_NAME> with the name of the OSS bucket that you selected in the Migrate to Professional Managed Kubernetes dialog box. 
                    ]
                }
        ```

        The following figure shows the modified policy content.

        ![Policy](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9260651261/p263342.png)

    After the migration is completed, the master nodes change to the **Unknown** state. You can perform the following steps to check the node states.

    1.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.
    2.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.
    3.  On the Nodes page, check the states of master nodes in the **Role/Status** column.

## Delete master nodes after the hot migration

After you migrate from a dedicated Kubernetes cluster to a professional managed Kubernetes cluster, you can delete the master nodes from the cluster. ACK does not allow you to delete master nodes in the console. You can run kubectl commands to delete master nodes.

Before you run commands, make sure that a kubectl client is connected to your cluster. For more information about how to use kubectl to connect to a cluster, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

1.  Run the following command to query the names of master nodes that you want to delete:

    ```
    kubectl get node | grep master
    ```

2.  Run the following command to delete a master node:

    ```
    kubectl delete node <MASTER_NAME>
    ```

    **Note:** Replace <MASTER\_NAME\> with the name of a master node that you obtained in the previous step.


## References

You can migrate from standard managed Kubernetes clusters to professional managed Kubernetes clusters. For more information, see [Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Migrate to professional managed Kubernetes clusters/Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters.md).

