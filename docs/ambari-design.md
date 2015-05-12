
# Ambari总体设计

## 抽象

### 资源

一个Service由多个ServiceComponent构成，一个ServiceComponent由多个ServiceComponentHost构成：

- **Service**: HDFS, YARN, HBase, etc

- **ServiceComponent**: HDFS.NameNode, YARN.ResourceManager, HBase.RegionServer, etc

- **ServiceComponentHost**: HDFS.NameNode.HostA, YARN.ResourceManager.HostB, etc

### 操作

操作抽象有三个，分别对应于上述的三种资源：

- **Operation**: Service层面的操作(Install/Start/Stop/Config)——一个Operation可以作用于一个或多个Service。

- **Task**: ServiceComponentHosts层面的操作——为了完成一个Operation，需要为不同的机器分配一系列的Task去执行。

- **Stage**: ServicesComponents层面的操作——根据不同ServicesComponents操作间的依赖关系，一个Operation的所有Task可能被划分成多个Stage，一个Stage内的多个Task相互没有依赖，可以并行执行。

```
需要特别说明操作的执行顺序：
1. 不同的Stage只能顺序执行。后面的Stage只有在前面Stage执行成功后才会下发给Agent。如果前面Stage失败，后面的Stage将取消。

2. 同一个Stage内的多个Task可以并行执行，可以同时下发给Agent。如果某个Task失败，其他的已下发且正执行的Task将被取消。

3. 分配给同一个机器的不同Task只会顺序执行。
```

下图描述了这三种资源与操作的对应关系：

![Abstraction][1]

  [1]: ../img/ambari-concepts.png

上述的三个操作抽象是定义态的描述，它们分别对应一个执行态的抽象：

- **StagePlan**: 执行态的Operation，是一个Stage DAG。

- **Command:** 执行态的Task，下发给具体的机器执行。主要有以下几种：
  
	- **ExecuteCommand:** 执行INSTALL/START/STOP等。

	- **StatusCommand:** 执行组件死活检查。

	- **CancelCommand:** 当stage中的某个task失败时，需要取消其他已经下发的task。

	- **RegistrationCommand:** 要求Agent向Server重新注册。

- **Action**: 执行态的Stage，由多个Command构成。

下图通过一个具体实例（启动HDFS和YARN服务），展示了其Stage DAG的构建逻辑：

![StagePlan][6]

  [6]: ../img/ambari-stageplan.png

## 架构

![Architecture][2]

  [2]: ../img/ambari-arch.png

### Metainfo

HDP服务的元数据可以通过一系列的文件来描述：

- **metainfo.xml:** 服务定义，包括服务名称、各个组件的定义、YUM安装包名、管控脚本路径等。

- **role_command_order.json:** 定义不同服务组件操作间的顺序依赖，用于构建Stage DAG。

- **metrics.xml:** 定义服务需要收集的指标。

除此之外，服务操作的执行需要定义一些运行环境，约定定义在如下几个文件夹：

- **package/scripts:** 包含服务操作的执行脚本（需要继承Script类，重载install/start/stop等方法）。

- **package/templates:** 包含自定义的模板文件。

- **package/files:** 包含自定义的任意文件。

- **configuration:** 包含服务的配置文件。这里的配置文件不会直接以文件形式下发到Agent，而是由Server解析为一个个的k/v property，在向Agent下发Command时，将这些k/v附加在其中。

```
说明：以上package下的所有文件夹将被Server打成运行包archive.zip，下发给Agent用于执行服务操作。
```

### Database State

	Current State + Action State = Desired State

- **Current State**: clusterstate, hostcomponentstate, hoststate

- **Desired State**: servicedesiredstate, servicecomponentdesiredstate, hostcomponentdesiredstate, 

- **Action State**: request, stage, host_role_command, execution_command

### Server主要模块

- **APIServer:** jetty server，对外提供REST资源操作API。

- **HeartbeatServer:** jetty server，对内agent提供心跳API。

- **MetainfoManager:** 读取和缓存服务的元数据。

- **ClusterController**: 统筹地处理资源操作请求。

- **ClusterFSM:** 集群状态机，在内存中维护各个资源的状态信息。
 
- **ResourceProvider:** 实现资源的CRUD操作，每种资源对应一个ResourceProvider。

- **PropertyProvider:** 向资源对象里添加各种property，主要是alert和metric两种。

- **StageBuilder:** 根据服务的元数据(比如由哪些组件构成、依赖哪些其他服务等)，构建Stage DAG，并生成每个Stage内的Task。

- **ActionQueue:** 缓存待执行的Stage。

### Agent主要模块

- **Controller:** 与Server端交互：通过heartbeat定期发送status report给server；从heartbeat返回中获取Command并分发出去。

- **ActionQueue:** 缓存待执行的Command。

- **CustomServiceOrchestrator:** 编排Commands的执行，包括获取脚本路径、构建脚本参数等。

- **PythonExecutor:** 执行python脚本。

- **CommandStatusDict:** 缓存脚本执行的状态。

## 工作流

### Server工作流

**变更操作**

![Server-Update-Workflow][3]

  [3]: ../img/server-update-workflow.png

**查询操作**

![Server-Get-Workflow][4]

  [4]: ../img/server-get-workflow.png

### Agent工作流

![Agent-Workflow][5]

  [5]: ../img/agent-workflow.png
