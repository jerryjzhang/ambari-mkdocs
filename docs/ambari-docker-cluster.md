## 单机一键安装大数据套件集群

在单机上用Docker容器搭建3台机器规模的集群，通过脚本一键安装。

### 用途

- 持续集成测试：每次发布新版本，自动搭建集群安装所有服务，全流程集成测试

- 快速功能测试：开发新特性需要做功能测试时，不需要找机器、重新搭建集群，测试完成后删除容器即可，无需清理环境

### 实现

- 集群搭建: 将Ambari打包成一个Docker镜像，基于该镜像启动三个容器，一个容器同时启动Ambari Server和Ambari Agent，另外两个只启动Agent。三个容器通过Pipework指定同网段的IP来组网。

- 安装服务: 编写Ambari Blueprint，声明需要安装哪些服务组件。再用Ambari Shell向Ambari Server提交Blueprint，实现自动安装与启动服务。

### 如何使用

- 登陆服务器

```
	ssh docker_common@10.151.140.110
```

- 启动TDP集群，安装所有服务

```
	/data/home/docker_common/jerryjzhang/ambari-cluster/startTDP.sh upgrade
```

- 启动TDP集群，只安装Metrics服务

```
   /data/home/docker_common/jerryjzhang/ambari-cluster/startMin.sh upgrade
```

- 等待启动命令返回后，访问套件界面

```
	http://10.151.140.110:8080
```

- 卸载集群

```
	stopCluster.sh
```