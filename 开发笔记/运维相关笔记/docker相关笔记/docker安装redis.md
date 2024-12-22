```
#拉取镜像
docker pull redis:latest
#启动镜像
docker run -itd --name redis -p 6379:6379 redis
```