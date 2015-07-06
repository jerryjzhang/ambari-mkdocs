## 如何生成License Key

- 登陆服务器

```
	ssh docker_common@10.151.140.110
```

- 执行脚本

```
    /data/home/docker_common/jerryjzhang/ambari-license/generateKey.sh <customerName> <clusterLimit> <expirationDate>
```

- 示例

```
   /data/home/docker_common/jerryjzhang/ambari-license/generateKey.sh jerryjzhang 50 2016/06/30
```