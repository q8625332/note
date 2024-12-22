## 安装 nacos

```
#拉取镜像
docker pull nacos/nacos-server
#启动镜像
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server

#设置内存 -e JVM_XMS=512m -e JVM_XMX=1024m
docker run --env MODE=standalone -e JVM_XMS=512m -e JVM_XMX=1024m --name nacos -d -p 8848:8848 nacos/nacos-server
```