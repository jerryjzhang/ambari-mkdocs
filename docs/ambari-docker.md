## 如何使用Dockerr容器启动Ambari Server

运行命令：

	docker run -d -h 10.151.140.110 -p 8441:8441 -p 8080:8080 -p 8440:8440 ambari/server:1.7.0

命令解释：

- -h <serverHostIP>，填写ambari-server所在服务器的IP

- 8080端口为ambari-server对外client的服务端口

- 8440和8441端口为ambari-server对内agent的服务端口
