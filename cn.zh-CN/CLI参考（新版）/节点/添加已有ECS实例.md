# 添加已有ECS实例

通过执行`arc-cs node attach <cluster_id> --password <password> --instances '["<instance_id1>","<instance_id2>"]'`命令添加已有ECS实例至ACK集群。

关于该命令的详细参数说明，请参见[添加已有实例到集群](/cn.zh-CN/API参考/节点/添加已有实例到集群.md)。

## CLI示例

请求示例：

```
arc-cs node attach c8cfc598fc84a41d6af96965f34ed**** \
--password Hello**** \
--instances '["i-bp1bw9dno0jghymp****","i-bp1bw9dno0jghymp****"]'
```

返回结果示例：

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

