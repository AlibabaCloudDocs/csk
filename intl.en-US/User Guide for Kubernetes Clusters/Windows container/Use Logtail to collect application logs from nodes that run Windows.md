---
keyword: [nodes that run Windows, Logtail, collect application logs]
---

# Use Logtail to collect application logs from nodes that run Windows

You can use Logtail to collect application logs from nodes that run Windows. This topic describes how to use Logtail to collect application logs from nodes that run Windows.

-   A Container Service for Kubernetes \(ACK\) cluster is created and **Enable Log Service** is selected when you created the cluster. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
-   [Create a node pool that runs Windows](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a node pool that runs Windows.md).

## Add Logtail to a node that runs Windows

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Find the alibaba-log-configuration ConfigMap in the kube-system namespace and add the following content to alibaba-log-configuration:

    ```
    win-log-config-path: C:\Program Files (x86)\Alibaba\Logtail\conf\{your region}\ilogtail_config.json
    ```

    **Note:** Replace the value of `{your region}` with the region where your ACK cluster is deployed.

3.  Run the following command to deploy DaemonSet on the node that runs Windows:

    ```
    apiVersion: extensions/v1beta1
    kind: DaemonSet
    metadata:
      labels:
        k8s-app: win-logtail-ds
      name: win-logtail-ds
      namespace: kube-system
    spec:
      selector:
        matchLabels:
          k8s-app: logtail-ds
          kubernetes.io/cluster-service: "true"
          version: v1.0
      template:
        metadata:
          annotations:
            scheduler.alpha.kubernetes.io/critical-pod: ""
          labels:
            k8s-app: logtail-ds
            kubernetes.io/cluster-service: "true"
            version: v1.0
        spec:
          containers:
          - env:
            - name: ALIYUN_LOGTAIL_CONFIG
              valueFrom:
                configMapKeyRef:
                  key: win-log-config-path
                  name: alibaba-log-configuration
            - name: ALIYUN_LOGTAIL_USER_ID
              valueFrom:
                configMapKeyRef:
                  key: log-ali-uid
                  name: alibaba-log-configuration
            - name: ALIYUN_LOGTAIL_USER_DEFINED_ID
              valueFrom:
                configMapKeyRef:
                  key: log-machine-group
                  name: alibaba-log-configuration
            - name: ALICLOUD_LOG_DOCKER_ENV_CONFIG
              value: "true"
            - name: ALICLOUD_LOG_ECS_FLAG
              value: "true"
            - name: ALICLOUD_LOG_DEFAULT_PROJECT
              valueFrom:
                configMapKeyRef:
                  key: log-project
                  name: alibaba-log-configuration
            - name: ALICLOUD_LOG_ENDPOINT
              valueFrom:
                configMapKeyRef:
                  key: log-endpoint
                  name: alibaba-log-configuration
            - name: ALICLOUD_LOG_DEFAULT_MACHINE_GROUP
              valueFrom:
                configMapKeyRef:
                  key: log-machine-group
                  name: alibaba-log-configuration
            - name: ALIYUN_LOG_ENV_TAGS
              value: _node_name_|_node_ip_
            - name: _node_name_
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: spec.nodeName
            - name: _node_ip_
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: status.hostIP
            - name: cpu_usage_limit
              value: "1.0"
            - name: mem_usage_limit
              value: "512"
            - name: max_bytes_per_sec
              value: "20971520"
            - name: send_request_concurrency
              value: "20"
            image: registry.cn-hangzhou.aliyuncs.com/log-service/winlogtail:ltsc2019-1.0.0.10
            imagePullPolicy: IfNotPresent
            name: logtail
            resources:
              limits:
                memory: 512Mi
              requests:
                cpu: 100m
                memory: 256Mi
            securityContext:
              privileged: false
            terminationMessagePath: /dev/termination-log
            terminationMessagePolicy: File
            volumeMounts:
            - mountPath: '\\\\.\pipe\docker_engine'
              name: sock
            - mountPath: 'c:\ProgramData\docker'
              name: root
              readOnly: true
            - mountPath: 'c:\logtail_host'
              name: root-c
              readOnly: true
          nodeSelector:
            beta.kubernetes.io/os: windows
          terminationGracePeriodSeconds: 30
          tolerations:
          - effect: NoSchedule
            key: os
            operator: Equal
            value: windows
          volumes:
          - hostPath:
              path: '\\\\.\pipe\docker_engine'
            name: sock
          - hostPath:
              path: 'c:\ProgramData\docker'
            name: root
          - hostPath:
              path: 'c:\'
            name: root-c
    ```

    **Note:** Logtail can collect only stdout files and import them to Alibaba Cloud Log Service. Logtail will soon support log file collection.


## Example

After you add Logtail to a node that runs Windows, use the following template to deploy Logtail:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: logtail-test
  name: logtail-test
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: logtail-test
      name: logtail-test
    spec:
      containers:
      - name: nanoserver
        image: mcr.microsoft.com/windows/servercore:1809
        command: ["powershell.exe"]
        args: ["ping -t 127.0.0.1 -w 10000"]
        env:
      ######### Specify environment variables ###########
        - name: aliyun_logs_logtail-stdout
          value: stdout
        - name: aliyun_logs_logttail-tags
          value: tag1=v1
      #################################
      nodeSelector:
        beta.kubernetes.io/os: windows
      tolerations:
      - effect: NoSchedule
        key: os
        operator: Equal
        value: windows
```

After the preceding application is deployed, you can view the log data. For more information, see [Query logs](/intl.en-US/Index and query/Query logs.md).

