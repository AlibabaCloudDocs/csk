---
keyword: [基于ECI和FaaS构建, 游戏战斗结算服]
---

# 基于ECI和FaaS构建游戏战斗结算服务

本文介绍基于ECI和FaaS构建游戏战斗结算服务的背景信息、架构图及操作参考链接。

在游戏行业的很多游戏类型中，尤其是SLG，为了防止客户端作弊，在每局战斗之后，在客户端预判玩家胜利的情况下，需要服务端来进行战斗数据的结算，从而确定玩家是不是真正的胜利。战斗结算是强CPU密集型，结算系统每日需要大量的计算力，尤其是开服或者活动期间忽然涌入的大量玩家，导致需要的计算量瞬间几倍增长，同时需要结算系统保持稳定的延时来保证玩家的用户体验。

## 架构图

![BP2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9958542261/p278937.png)

## 参考链接

有关基于ECI和FaaS构建游戏战斗结算服务的详情，请参见[基于ECI+FaaS构建游戏战斗结算服务](https://bp.aliyun.com/detail/182)。

