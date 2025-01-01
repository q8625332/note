
**拉取镜像**

```
docker pull uozi/nginx-ui
```

**运行**

```
docker run -dit \
  --name=nginx-ui \
  --restart=always \
  -e TZ=Asia/Shanghai \
  -v /home/nginx:/etc/nginx \
  -v /home/nginx-ui:/etc/nginx-ui \
  -v /var/www:/var/www \
  -p 8080:80 -p 8443:443 \
  uozi/nginx-ui:latest
```