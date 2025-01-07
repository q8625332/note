
**拉取镜像**

```
docker pull jeessy/ddns-go
```

**运行**

```
docker run -d \
--name ddns-go \
--restart=always \
-p 9876:9876 \
-v /home/ddns-go:/root \
jeessy/ddns-go
```

**重置密码**

```
docker exec ddns-go ./ddns-go -resetPassword 123456
docker restart ddns-go
```