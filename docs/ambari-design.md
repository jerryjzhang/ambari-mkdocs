
# 核心概念

## Resource

- Service: HDFS, YARN, HBase, etc

- ServiceComponent: HDFS.NameNode, YARN.ResourceManager, HBase.RegionServer, etc

- ServiceComponentHost: HDFS.NameNode.HostA, YARN.ResourceManager.HostB

一个Service由多个ServiceComponent构成，一个ServiceComponent由多个ServiceComponentHost构成。

## 术语

- Operation: Service层面的操作(Install/Start/Stop/Config)

- Stage: ServicesComponents层面的操作

- StagePlan: 规划一次operation的stage顺序

- Action: 执行态的stage

- Task/Request: ServiceComponentHosts层面的操作

一次Operation包含的Stage遵照StagePlan顺序执行，一个Stage包含的Task可以并行执行。