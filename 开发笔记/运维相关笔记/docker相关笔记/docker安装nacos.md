## 安装 nacos

```
#拉取镜像
docker pull nacos/nacos-server
#启动镜像
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server

#设置内存 --memory="512m"
docker run -d --name nacos-server --memory="512m" -e MODE=standalone -p 8848:8848 nacos/nacos-server:latest
```