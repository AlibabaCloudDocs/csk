# Remove nodes from a cluster

You can run the following command to remove nodes from a cluster: `arc-cs node delete <instance_id> --release-node true --nodes '["<region>.<instance_ip1>","<region>.<instance_ip2>"]`.

For more information about this command, see [DeleteClusterNodes](/intl.en-US/API Reference/Node pools/DeleteClusterNodes.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs node delete c8cfc598fc84a41d6af96965f34ed**** \
--release-node true \
--nodes '["cn-hangzhou.10.1.xxx.xxx","cn-hangzhou.10.1.xxx.xxx"]
```

Sample response:

```
{
    "list": [
        {
            "code": "200",
            "instanceId": "i-2zee3oiwcyoz7kwd****",
            "message": "successful"
        },
        {
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbt****",
            "message": "successful"
        }
    ],
    "task_id": "T-5a544aff80282e39ea000039"
}
```

