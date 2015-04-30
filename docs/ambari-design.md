
# Ambari总体设计

## 核心概念

### Resource

一个Service由多个ServiceComponent构成，一个ServiceComponent由多个ServiceComponentHost构成：

- **Service**: HDFS, YARN, HBase, etc

- **ServiceComponent**: HDFS.NameNode, YARN.ResourceManager, HBase.RegionServer, etc

- **ServiceComponentHost**: HDFS.NameNode.HostA, YARN.ResourceManager.HostB

### Operation

一次Operation包含的Stage遵照StagePlan顺序执行，一个Stage包含的Task可以并行执行：

- **Operation**: Service层面的操作(Install/Start/Stop/Config)

- **Stage**: ServicesComponents层面的操作

- **StagePlan**: 规划一次operation的stage顺序

- **Action**: 执行态的stage

- **Task/Request**: ServiceComponentHosts层面的操作

![Abstraction][1]

  [1]: ../img/ambari-concepts.png

## 架构

![Architecture][2]

  [2]: ../img/ambari-arch.png

### Metainfo

HDP服务的元数据通过如下几类文件来定义：

- **metainfo.xml:** 服务定义，包括服务名称、各个组件的定义、YUM安装包名、管控脚本路径等

- **metrics.xml:** 定义服务需要收集的指标

- **scripts:** 包含服务的管控脚本

- **configuration:** 包含服务的配置文件，所有配置项按统一的property格式加载

- **templates:** 包含自定义的模板文件

- **files:** 包含自定义的任意文件

scripts、templates以及files文件夹包含的所有文件将被server打包，下发给agent用于执行操作。

### Database State

- **Current State**: clusterstate, hostcomponentstate, hoststate

- **Desired State**: servicedesiredstate, servicecomponentdesiredstate, hostcomponentdesiredstate, 

- **Action State**: host_role_command, execution_command