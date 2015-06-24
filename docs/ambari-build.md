## 如何编译Ambari源码

### 登陆服务器

	ssh docker_common@10.151.140.110

### 启动Build容器

	/data/home/docker_common/jerryjzhang/ambari-build/start-build.sh <ambari-sourceDir>

### 转换源文件格式

	for i in `find <sourceBaseDir> -type f -name "*ambari*" -or -name "*.py" -or -name "*.sh" -or -name "*.java" |grep -v .git | grep -v target |grep -v jar|grep -v png`;do dos2unix $i; done

### Maven编译 - 1.7.0

	mvn versions:set -DnewVersion=1.7.0.0
	mvn install rpm:rpm -Dfindbugs.skip -DskipTests -Drat.skip -Dmaven.test.skip -DskipAssembly -DnewVersion=1.7.0.0 -Dpython.ver="python >= 2.6"

### Maven编译 - 2.0.0

	mvn versions:set -DnewVersion=2.0.0.0
	pushd ambari-metrics
	mvn versions:set -DnewVersion=2.0.0.0
	popd
	mvn install rpm:rpm -Dfindbugs.skip -DskipTests -Drat.skip -Dmaven.test.skip -DskipAssembly -DnewVersion=2.0.0.0 -Dpython.ver="python >= 2.6"

## 附录

### 容器启动脚本内容

```bash
#!/bin/bash

export AMBARI_BUILD_HOME=/data/home/docker_common/jerryjzhang/ambari-build

echo 'Building $1'

docker run -it --rm -v $1:/ambari \
    -v $AMBARI_BUILD_HOME/m2:/root/.m2 \
    -v $AMBARI_BUILD_HOME/ambari-web/node_modules:/ambari/ambari-web/node_modules \
    -v $AMBARI_BUILD_HOME/ambari-admin/node_modules:/ambari/ambari-admin/src/main/resources/ui/admin-web/node_modules \
    -v $AMBARI_BUILD_HOME/ambari-admin/node:/ambari/ambari-admin/src/main/resources/ui/admin-web/node \
    -w /ambari \
    --entrypoint /bin/bash \
    docker.oa.com:8080/ambari/build:1.7.0
```