
**拉取镜像**

```
docker pull jeessy/ddns-go
```

**运行**

```
docker run -d --name ddns-go \
--restart=always \
-p 9876:9876 \
-v /opt/ddns-go:/root \
jeessy/ddns-go
```