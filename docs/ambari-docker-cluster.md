## 单机一键安装大数据套件集群

在单机上用Docker容器搭建3台机器规模的集群，通过脚本一键安装。

### 用途

- 持续集成测试：每次发布新版本，自动搭建集群安装所有服务，全流程集成测试

- 快速功能测试：开发新特性需要做功能测试时，不需要找机器、重新搭建集群，测试完成后删除容器即可，无需清理环境

### 如何使用

1. 登陆服务器

```
	ssh docker_common@10.151.140.110
```

2. 启动TDP集群，安装所有服务

```
	/data/home/docker_common/jerryjzhang/ambari-cluster/startTDPUpgrade.sh
```

   启动TDP集群，只安装Metrics服务

```
   /data/home/docker_common/jerryjzhang/ambari-cluster/startMin.sh
```

3. 等待启动命令返回后，访问套件界面

```
	http://10.151.140.110:8080
```