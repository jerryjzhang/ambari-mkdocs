### 前台有哪些地方需要做汉化？

- 通用文字提示：ambari-web/app/messages.js

- Admin界面的文字提示：ambari-admin/src/main/resources/ui/admin-web/app/views文件夹下的所有HTML文件

- 选择服务的描述文字：ambari-server/src/main/resources/stacks/HDP/*/services下的每个metainfo.xml文件

### 为何偶尔出现在前台停止服务后，过会又自动启动的情况？

Server会定期(默认周期1分钟)调用服务脚本的STATUS命令，如果返回结果是OK，Server端会认为服务组件处于STARTED状态。观察以下日志佐证：

**用户请求停止服务组件:**

	AmbariManagementControllerImpl:2175 - Received a updateHostComponent request, clusterName=tdw, serviceNa
	me=LHOTSE, componentName=RUNNER_SLAVE, hostname=10.149.27.91, request={ clusterName=tdw, serviceName=LHOTSE, componentName=RUNNER_SLAVE, host
	name=10.149.27.91, desiredState=INSTALLED, desiredStackId=null, staleConfig=null, adminState=null}

**服务组件重新被设置成STARTED状态:**

	HeartBeatHandler:464 - State of service component RUNNER_SLAVE of service LHOTSE of cluster tdw has changed from INSTALLED to STARTED at host 10.149.27.91

### 为何会前台经常出现服务需要重启的提示？

以下几种情况下会出现重启提示：

- 每次新增/移除一个ServiceComponentHost，都需要将所有monitringService的master组件和对应机器上的slave组件重启。是否是monitoringService可以在服务的metaInfo.xml中定义。**目前只有nagios和ganglia属于此类。**

- 每次移除一个ServiceComponentHost，如果其所属的服务有restartRequiredAfterChange属性(在metaInfo.xml定义)，需要把其master组件重启。**目前只有zookeeper和kafka属于此类。**

- 每次更新某个配置，如果其配置类型(configType)是服务或组件依赖的(在metainfo.xml中通过configuration-dependencies配置)，则服务或组件需要重启。注：对于global这个特殊的configType，只有当其中的key被服务定义过，才会触发该服务重启。**目前只有HDFS/YARN/MAPREDUCE2共同依赖core-site配置类型。**

### Server端的服务运行包是在何时通过什么机制同步到Agent端的？

1. Server每次启动时会将所有服务的package文件夹内容(包括scripts,templates,files)打成archive.zip，同时package下生成一个hash文件。

2. Agent执行某个服务操作时，如果发现updateToDate列表里没有该服务，则会检查本地缓存的运行包hash与Server端的hash进行比较，如果不匹配，则从Server端下载archive.zip，然后将服务添加进updateToDate列表里。

3. 每次Agent重新向Server注册时，会将updateToDate清空。

因此，如果改动了Server端的某个文件，想要同步到Agent端，需要重启Server，但不必要重启Agent（Agent会重新向Server注册）。

### Ambari 2.0为何需要重新设计指标收集模块

以前的指标收集依赖于Ganglia，在性能、扩展性、易用性等方面存在一定限制：

1. Ganglia无法横向扩展到大规模集群。比如，Gmond与Gmetad之间的通讯机制比较重、Gmond在每个机器上需要启动多个监控进程等。

2. Ganglia使用RRD文件数据库来存储指标，无法提供SQL查询、聚合查询、历史查询等。

3. 为一个新Ambari服务添加指标比较麻烦，需要同时配置Ganglia和Ambari的相关文件。

4. Ganglia没有可插拔的存储模块，很难将指标数据接入其他外部存储系统。

### Ambari 2.0为何需要重新设计监控告警模块

以前的监控告警依赖于Nagios，在性能、扩展性、易用性等方面存在一定限制：

1. Nagios将告警数据存储到一个status.dat文件中，每次读取告警又需要打开与解析文件，性能差。

2. Nagios无法提供SQL查询、聚合查询和历史查询。

3. 为一个新Ambari服务添加告警比较麻烦，需要同时配置Nagios和Ambari的相关文件。

4. 完全依赖Nagios将很难将告警数据与其他告警系统整合。
