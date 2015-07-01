## 单机一键安装大数据套件集群

### 用途

- 持续集成测试：每次发布新版本，自动搭建集群安装所有服务，全流程集成测试

- 快速功能测试：开发新特性需要做功能测试时，不需要找机器、重新搭建集群

- 集群容器化的探索：集群容器化带来很多好处——快速部署、运行环境无依赖、卸载无痕迹、共享机器资源

### 如何使用

1. 登陆服务器

```
	ssh docker_common@10.151.140.110
```

2. 启动TDP集群，安装所有服务

```
	/data/home/docker_common/jerryjzhang/ambari-cluster/startTDP.sh
```

   启动TDP集群，只安装Metrics服务

```
   /data/home/docker_common/jerryjzhang/ambari-cluster/startMin.sh
```