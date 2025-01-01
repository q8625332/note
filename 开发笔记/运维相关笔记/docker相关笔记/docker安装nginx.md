
**拉取镜像**

```
docker pull nginx:1.25
```

**创建本地挂载目录**

```
// 先创建nginx目录（以下是示例路径）
[root@192 home]# mkdir nginx
[root@192 home]# cd nginx/
// 创建配置文件挂载
[root@192 nginx]# mkdir conf
// 创建静态文件挂载
[root@192 nginx]# mkdir html
// 创建日志文件挂载
[root@192 nginx]# mkdir log
// 列出目录
[root@192 nginx]# ls
conf  html  log
```

**将所需文件在容器中复制出来**

>因为挂载时，如果宿主机挂载目录为空，则可能会将容器中挂载的目录给覆盖

```
docker run -d nginx:1.25
```

**获取挂载文件**

```
//将容器nginx.conf文件复制到宿主机
docker cp 容器id:/etc/nginx/nginx.conf /home/nginx/conf/nginx.conf

//将容器conf.d文件夹复制到宿主机
docker cp 容器id:/etc/nginx/conf.d /home/nginx/conf/conf.d

//将容器静态文件夹html复制到宿主机
docker cp 容器id:/usr/share/nginx/html/ /home/nginx
```

**停止容器，并删除**

```
// 停止容器，ce869a为容器id
docker stop 容器id

//删除容器
docker rm 容器id
```

**运行nginx**

```
docker run -d -p 80:80 \
--name nginx \
--privileged \
--restart always \
-v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /home/nginx/html:/usr/share/nginx/html \
-v /home/nginx/log:/var/log/nginx \
nginx
```