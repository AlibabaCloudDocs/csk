# Scenarios {#concept_prx_jtv_tdbaa .concept}

## DevOps continuous delivery {#section_r24_g3w_tdb .section}

**Optimal continuous delivery process**

Working with Jenkins, Container Service encompasses the complete process of DevOps from code submission to application deployment in an automated fashion. It makes sure that only codes passing the automated test can be delivered and deployed, and efficiently replaces the traditional method of complicated deployment and slow iteration in the industry.

**Container Service can implement:**

-   Automation of DevOps.

    The automation of the full process from code changes to code building, image building, and application deployment.

-   Consistency of environment.

    Container Service allows you to deliver not only code but also a running environment based on immutable architecture.

-   Continuous feedback

    Results of each integration or delivery are fed back in real time.


**We recommend that you use**

Elastic Compute Service \(ECS\) and Container Service together.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16052/15362222617271_en-US.png)

## Microservice architecture {#section_x24_g3w_tdb .section}

**Implement agile development and deployment to accelerate business iteration of enterprises**

In the production environment of enterprises, microservices are divided reasonably and each microservice application is stored in the Alibaba Cloud image repository.  You only have to iterate each microservice application, and Alibaba Cloud provides the capabilities of scheduling, orchestration, deployment, and gated launch.

**Container Service can implement:**

-   Server Load Balancer and service discovery.

    Supports Layer-4 and Layer-7 request forwarding and backend binding.

-   Many policies of scheduling and exception recovery.

    Supports affinity scheduling at the level of services. Supports cross-zone high-availability and disaster recovery.

-   Microservice monitoring and auto scaling.

    Supports the monitoring at the level of microservices and containers. Supports auto scaling of microservices.


**We recommend that you use **

ECS, Relational Database Service \(RDS\), Object Storage Service \(OSS\), and Container Service together.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16052/15362222617273_en-US.png)

## Hybrid cloud architecture {#section_af4_g3w_tdb .section}

**Unified Operation and Maintenance of multiple cloud resources**

Manage resources on and off the cloud at the same time in the Container Service console, without switching between multiple cloud consoles.  Deploy applications on and off the cloud at the same time by using the same image and orchestration based on the characteristics unrelated to the container infrastructure.

**Container Service supports:**

-   Scaling in and out applications on the cloud.

    Expand the capacity rapidly on the cloud at the business peak period to bring some business traffic to the cloud.

-   Disaster recovery on the cloud.

    Deploy business systems on and off the cloud at the same time to provide services off the cloud and provide disaster recovery on the cloud.

-   Development and test off the cloud.

    Release the applications seamlessly on the cloud after the development and test off the cloud.


**We recommend that you use **

ECS, Virtual Private Cloud \(VPC\), and Express Connect together.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16052/15362222617274_en-US.png)

## Auto scaling architecture {#section_df4_g3w_tdb .section}

**Automatic expansion/contraction for the business according to the business traffic**

Container Service can automatically expand or contract the business according to the business traffic, without manual intervention. In this way, the system is not down because of traffic surge and not timely expansion, and the waste due to a large number of idle resources is avoided.

**Container Service can implement:**

-   Rapid response.

    Trigger the container expansion in seconds when the business traffic reaches the expansion indicator.

-   Full automation.

    The expansion or contraction process is fully automated, without manual intervention.

-   Low cost.

    Contract the capacity automatically when the traffic is reduced to avoid the waste of resources.


**We recommend that you use **

ECS and CloudMonitor together.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16052/15362222617275_en-US.png)

