## 如何连接ambari-server的postgres数据库

docker run -it --rm docker.oa.com:8080/docker/postgres sh -c 'exec psql -h "10.149.27.95" -p "5432" -U ambari'

其中：

- 10.149.27.95为ambari-server所在的机器IP

- 5432是postgres固定端口

- ambari是用户名，密码为bigdata