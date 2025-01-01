
**拉取镜像**

```
docker pull nginx
```

**创建本地挂载目录**



```
docker run -d -p 80:80 \
--name nginx --privileged \
--restart always \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /home/nginx/html:/usr/share/nginx/html \
-v /home/nginx/log:/var/log/nginx \
nginx

```