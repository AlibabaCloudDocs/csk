# Add a custom route

If you want to configure multiple domain names for a Knative service, you can specify the custom domain names and paths in the `knative.aliyun.com/serving-ingress` annotation. After you modify the annotation, you can use the custom domain names and paths to access the Knative service. This topic describes how to add a custom domain name and a path for a Knative service, and use the custom domain name and path to access the Knative service.

Knative has a default primary domain name. Each Knative service has a unique domain name that is generated based on the primary domain name of Knative, the namespace, and the service name. The format of a domain name is \{ksvc-name\}.\{namespace\}.\{knative-default-domain\}. The default primary domain name of Knative is example.com. If a Knative service named coffee is deployed in the default namespace, the default domain name of the Knative service is coffee.default.example.com. You can modify the default primary domain name of Knative. For more information, see [Set up a custom domain name for Knative Serving](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Use a custom domain name in Knative.md).

If you want to configure multiple domain names for a Knative service, specify custom domain names and paths in the `knative.aliyun.com/serving-ingress` annotation. For example, if the annotation is modified to `knative.aliyun.com/serving-ingress: cafe.mydomain.com/coffee`, it indicates that the custom domain name of the service is cafe.mydomain.com and the path is /coffee.

**Note:** The custom domain name and path do not overwrite the default domain name. Both the default and custom domain names are available.

## Procedure

1.  Copy the following content to the ingress-domain.yaml file:

    ```
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: coffee-mydomain
      annotations:
        knative.aliyun.com/serving-ingress: cafe.mydomain.com/coffee
    spec:
      template:
          spec:
          containers:
            - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/helloworld-go:160e4dc8
    ```

2.  Create a Knative service.

    ```
    kubectl apply -f ingress-domain.yaml
    ```

3.  View information about the Knative service.

    The output shows that the default domain name of the Knative service is coffee-mydomain.default.example.com.

    ```
    kubectl get ksvc
    ```

    ```
    NAME              URL                                          LATESTCREATED           LATESTREADY             READY   REASON
    coffee-mydomain   http://coffee-mydomain.default.example.com   coffee-mydomain-sbwhz   coffee-mydomain-sbwhz   True
    ```

4.  Use the default domain name to access the Knative service.

    ```
    curl -H "Host: coffee-mydomain.default.example.com" http://106.15.2**. **
    ```

    ```
    Hello World!
    ```

5.  Use the custom domain name and path to access the Knative service.

    ```
    curl -H "Host: cafe.mydomain.com" http://106.15.2**.**/coffee
    ```

    ```
    Hello World!
    ```


