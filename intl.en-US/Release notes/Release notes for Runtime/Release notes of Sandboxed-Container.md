---
keyword: [Sandboxed-Container, release notes]
---

# Release notes of Sandboxed-Container

This topic lists the latest changes to the Sandboxed-Container runtime.

For more information about Sandboxed-Container, see [Sandboxed-Container overview](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md).

## April 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.2.0|2021-04-02|The secure computing mode \(Seccomp\) feature is enabled for the containerd runtime. **Note:** The Seccomp feature is supported by clusters of Kubernetes V1.20 or later.

|No impact on workloads.|

## March 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.1.2|2021-03-01|The issue where exceptions occur in privileged containers in some scenarios is fixed.|No impact on workloads.|

## January 2021

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.1.1|2021-01-07|Privileged containers are supported.|No impact on workloads.|

## December 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.1.0|2020-11-26|New features are released to improve service stability and performance. New features:-   A project quota is supported to limit the number of bytes that can be written to the container rootfs directory.
-   A disk can be mounted to a sandboxed container.
-   An Apsara File Storage NAS \(NAS\) file system can be mounted to a sandboxed container.
-   Custom kernel parameters are supported for sandboxed pods.
-   Quality of Service \(QoS\) policies and network traffic marking policies are supported.

|No impact on workloads.|

## August 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|2.0.0|2020-08-28|Sandboxed-Container V2.0 is released to achieve the following benefits:-   Sandboxed-Container V2.0 is a container runtime developed by Alibaba Cloud on top of lightweight virtual machines. This version supports more lightweight and efficient deployment, and simplifies the architecture and maintenance of Kubernetes clusters.
-   Reduces the resource overheads by 90% and accelerates the startup of sandboxed containers by three times.
-   Increases the deployment density of standalone sandboxed containers by 10 times.
-   The virtio-fs file system is supported. The performance of this file system is higher than the performance of the 9pfs file system.

|During the upgrade, the pods on the nodes that use the Sandboxed-Container runtime are recreated. Pay attention to pod redundancy.|

## July 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.1.1|2020-07-27|The following issues that are related to the stability of Sandboxed-Container are fixed:-   The security risk that is related to the container-storaged component is eliminated.
-   The issue where the `kubectl cp` command is blocked after you run this command is fixed.
-   The issue where logs cannot be printed to stdout files after containerd is restarted is fixed.
-   The issue where the system time of sandboxed containers may not be synchronized at regular intervals is fixed.

|No impact on workloads.|

## March 2020

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.1.0|2020-03-05|New features of Sandboxed-Container V1.1.0 are released: -   Alibaba Cloud disks and NAS file systems can be mounted to sandboxed containers. This provides the same performance as the volumes that are mounted to the host and avoids performance loss when storage devices are mounted over 9pfs.
-   RootFS block I/O throttling is supported.

The stability of Sandboxed-Container V1.1.0 is significantly improved.

|No impact on workloads.|

## September 2019

|Version|Release date|Description|Impact|
|-------|------------|-----------|------|
|1.0.0|2019-09-05|The following features of Sandboxed-Container V1.0.0 are supported: -   Strong isolation based on sandboxed and lightweight virtual machines.
-   Compatibility with runC in terms of application management.
-   High performance that is equivalent to 90% of the performance provided by applications based on runC.
-   The same user experience as runC in terms of logging, monitoring, and storage.
-   The RuntimeClass feature is released. This feature allows you to select container runtimes such as runC and runV. For more information, see [RuntimeClass](https://kubernetes.io/docs/concepts/containers/runtime-class/).
-   Ease of use with minimum technical skill requirements.
-   Higher stability compared with the open source Kata Containers runtime. For more information about Kata Containers, see [Kata Containers](https://katacontainers.io/).

|No impact on workloads.|

**Related topics**  


[Containerd release notes](/intl.en-US/Release notes/Release notes for Runtime/Containerd release notes.md)

[Docker release notes](/intl.en-US/Release notes/Release notes for Runtime/Docker release notes.md)

