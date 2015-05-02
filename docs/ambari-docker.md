# Docker方式搭建Ambari

## 准备工作

1. 3.10版本及以上的Linux内核

2. Docker二进制可执行文件

3. Ambari Server和HDP Repository的Docker镜像文件，分别是ambari-server-1.7.0.tar和ambari-hdprepo-2.2.tar

## 启动Docker Daemon

	chmod +x ./docker
	sudo ./docker -d &

## 导入Docker镜像

	sudo ./docker load < ambari-server-1.7.0.tar
	sudo ./docker load < ambari-hdprepo-2.2.tar

## 启动Ambari Server容器
	
运行以下命令：

	sudo ./docker run -d -h <serverHostIP> -p 8441:8441 -p 8080:8080 -p 8440:8440 ambari/server:1.7.0

命令解释：

- -h <serverHostIP>，填写ambari-server所在服务器的IP

- 8080端口为ambari-server对外client的服务端口

- 8440和8441端口为ambari-server对内agent的服务端口

## 启动HDP Repository容器

运行以下命令：

	sudo ./docker run -d -h <serverHostIP> -p 80:80 ambari/hdprepo:2.2

命令解释：

- -h <serverHostIP>，填写ambari-server所在服务器的IP

- 80端口为HDP repository对外的HTTP服务端口

