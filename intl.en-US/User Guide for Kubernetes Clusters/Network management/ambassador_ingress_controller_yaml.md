# Ingress management with Ambassador Edge Stack

  This tutorial covers the use of Ambassador Edge Stack (AES) to manage ingress traffic to your services.

  [Ambassador Edge Stack](https://getambassador.io/) is a popular, high-performance [Ingress Controller](https://www.getambassador.io/learn/kubernetes-glossary/ingress-controller/) and [API Gateway](https://www.getambassador.io/learn/kubernetes-glossary/api-gateway/) built on [Envoy Proxy](https://www.envoyproxy.io/). Envoy Proxy was designed from the ground up for [cloud-native](https://www.getambassador.io/learn/kubernetes-glossary/cloud-native/) applications. Ambassador exposes Envoy's functionality as [Custom Resource Definitions](https://www.getambassador.io/learn/kubernetes-glossary/custom-resource-definition/), with integrated support for rate limiting, authentication, load balancing, and observability.

  Alibaba Cloud Service does not provide support for AES deployment. You are responsible for configuring, updating, and managing your system. The following sections help you get started, but you are responsible for any changes that you make to the steps.  For additional help and support, or to contribute to the Ambassador project itself, you can explore [the Ambassador documentation and community](https://www.getambassador.io/docs/latest/).

# Requirements:

  1. Kubectl CLI \[[get it here](https://kubernetes.io/docs/tasks/tools/install-kubectl/)\]
  2. Alibaba Standard Dedicated Cluster \[[get it here](https://www.alibabacloud.com/help/doc-detail/85903.htm?spm=a2c63.l28256.b99.48.a2567d1bDPLQKX)\]

# Installation and Deployment

  First we are going to deploy Ambassador Edge Stack to a Standard Dedicated Alibaba Kubernetes cluster, if you haven't created a cluster, visit the [getting started page](https://www.alibabacloud.com/help/doc-detail/85903.htm?spm=a2c63.l28256.b99.48.a2567d1bDPLQKX) to learn how.  Ensure that you are working on the right cluster by running `kubectl config current-context`.  There are multiple ways of deploying AES, this tutorial covers installing the [Ambassador Operator](https://www.getambassador.io/docs/latest/topics/install/aes-operator/) via a manual `yaml` deployment.  We will then use the Operator to install Ambassador Edge Stack to the cluster.  You can explore other installation methods at Ambassador's [installation page](https://www.getambassador.io/docs/latest/topics/install/).

  Installing Ambassador via the Operator provides a more robust workflow by automating and integrating the Ambassador installation and upgrade process.  The Operator also allows for additional configuration options by applying Helm chart values.

### Installation

  1. Apply the Operator CRDs.

      ```bash
      kubectl apply -f https://github.com/datawire/ambassador-operator/releases/latest/download/ambassador-operator-crds.yaml
      ```

  2. Apply the Operator deployment manifest.

      ```bash
      kubectl apply -n ambassador -f https://github.com/datawire/ambassador-operator/releases/latest/download/ambassador-operator.yaml
      ```

  3. Create an "AmbassadorInstallation" deployment manifest.

      ```yaml
      ---
      apiVersion: getambassador.io/v2
      kind: AmbassadorInstallation
      metadata:
        name: ambassador
      spec:
        version: "*"
      ```

  4. Apply the manifest.

      ```bash
      kubectl apply -n ambassador -f installation.yaml
      ```

  This tutorial only covers a basic Ambassador installation via Operator.  To learn more about the Ambassador Operator and its additional configurations, you can explore the [installation page](https://www.getambassador.io/docs/latest/topics/install/aes-operator/).

  ### Exposing a Service

  To test our deployment, we are going to deploy a sample application to deliver random quotes to users.  The manifest below describes a Quote deployment exposed via a Quote service.

  ```yaml
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: quote
  spec:
    ports:
    - name: http
      port: 80
      targetPort: 8080
    selector:
      app: quote
  ---
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: quote
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: quote
    strategy:
      type: RollingUpdate
    template:
      metadata:
        labels:
          app: quote
      spec:
        containers:
        - name: backend
          image: quay.io/datawire/quote:0.3.0
          ports:
          - name: http
            containerPort: 8080
  ```

  1. Apply with `kubectl apply -f quote.yaml`.

  2. We have our deployment and it's exposed with a service, but we still cannot connect to it without telling Ambassador where it is.  This is where the [Mapping](https://getambassador.io/docs/latest/topics/using/intro-mappings/) comes into play.  With a mapping, we can use kube-dns to let Ambassador know exactly where to look for the Quote service when the right request comes in.

      ```yaml
      ---
      apiVersion: getambassador.io/v2
      kind: Mapping
      metadata:
        name: backend
      spec:
        prefix: /backend/
        service: quote
      ```

  3. Apply the mapping with `kubectl apply -f quote-backend.yaml`.
  
  4. Test that it works by using `curl -k https://{{AMBASSADOR_IP}}/backend/` in your terminal.  You should see something similar to the following:

      ```bash
      {
          "server": "bleak-kumquat-n9qg6ra1",
          "quote": "Non-locality is the driver of truth. By summoning, we vibrate.",
          "time": "2020-05-08T18:33:54.578661743Z"
      }% 
      ```