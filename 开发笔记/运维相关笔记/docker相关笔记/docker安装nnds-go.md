
**拉取镜像**

```
docker pull jeessy/ddns-go
```

**运行**

```
docker run -d --name ddns-go \
--restart=always \
--net=host \
-v /opt/ddns-go:/root \
jeessy/ddns-go
```