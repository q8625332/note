## 安装 nacos

```
#拉取镜像
docker pull nacos/nacos-server
#启动镜像
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server

#设置内存 启动的最小内存为-e JVM_XMS=60m -e JVM_XMX=80m
docker run --env MODE=standalone -e JVM_XMS=60m -e JVM_XMX=80m --name nacos -d -p 8848:8848 -p 9848:9848 nacos/nacos-server
```