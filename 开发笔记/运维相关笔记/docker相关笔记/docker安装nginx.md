
**拉取镜像**

```
docker pull nginx
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
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
                        
原文链接：https://blog.csdn.net/SmallCat0912/article/details/132061625
```

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