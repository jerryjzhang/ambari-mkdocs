## 如何使用Ambari Shell操作集群

### 启动Shell容器

填写对应的Ambari Server的IP和端口：

	docker run -it --rm sequenceiq/ambari-shell --ambari.host=10.149.27.95 --ambari.port=8080

输入命令：

	services list