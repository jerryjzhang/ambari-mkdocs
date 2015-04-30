### 前台有哪些地方需要做汉化？

- 通用文字提示：ambari-web/app/messages.js

- Admin界面的文字提示：ambari-admin/src/main/resources/ui/admin-web/app/views文件夹下的所有HTML文件

- 选择服务的描述文字：ambari-server/src/main/resources/stacks/HDP/*/services下的每个metainfo.xml文件

### 为何偶尔出现在前台停止服务后，过会又自动启动的情况？

Server会定期调用服务脚本的STATUS命令，如果返回结果是OK，Server端会认为服务组件处于STARTED状态。观察以下日志佐证：

**用户请求停止服务组件:**

	AmbariManagementControllerImpl:2175 - Received a updateHostComponent request, clusterName=tdw, serviceNa
	me=LHOTSE, componentName=RUNNER_SLAVE, hostname=10.149.27.91, request={ clusterName=tdw, serviceName=LHOTSE, componentName=RUNNER_SLAVE, host
	name=10.149.27.91, desiredState=INSTALLED, desiredStackId=null, staleConfig=null, adminState=null}

**服务组件重新被设置成STARTED状态:**

	HeartBeatHandler:464 - State of service component RUNNER_SLAVE of service LHOTSE of cluster tdw has changed from INSTALLED to STARTED at host 10.149.27.91

### 为何会前台经常出现服务需要重启的提示？

以下几种情况下会出现重启提示：

- 每次新增/移除一个ServiceComponentHost，都需要将所有monitringService的master组件和对应机器上的slave组件重启。是否是monitoringService可以在服务的metaInfo.xml中定义，目前只有nagios和ganglia属于此类。

- 每次新增/移除一个ServiceComponentHost，如果其所属的服务有restartRequiredAfterChange属性(在metaInfo.xml定义)，需要把其master组件重启。

- 每次修改服务的配置，保存后会出现重启提示。
