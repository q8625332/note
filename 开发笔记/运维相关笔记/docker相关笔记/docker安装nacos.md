## 安装 nacos

```
#拉取镜像
docker pull nacos/nacos-server
#启动镜像
docker run --env MODE=standalone --name nacos -d -p 8848:8848 nacos/nacos-server
```