
**拉取镜像**

```
docker pull nginx
```

**创建本地挂载目录**



```
docker run -d -p 80:80 \
--name nginx --privileged \
--restart always \
-v /root/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /root/nginx/conf/conf.d:/etc/nginx/conf.d -v /root/nginx/html:/usr/share/nginx/html -v /root/nginx/log:/var/log/nginx docker.io/nginx:1.25

```