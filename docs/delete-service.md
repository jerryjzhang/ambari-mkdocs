## 怎样删除已安装服务

### 登陆服务器

	ssh docker_common@10.151.140.110

### 运行rest-shell

	~/jerryjzhang/rest-shell-1.2.1.RELEASE/bin/rest-shell

### 输入Shell命令

(先确保服务所有组件处于停止状态)

	baseUri http://10.149.27.95:8080/api/v1
	auth basic --username admin --password admin
	headers set --name X-Requested-By --value jerryjzhang
	delete clusters/tdw/services/LHOTSE

