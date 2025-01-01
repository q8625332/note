
**拉取镜像**

```
docker pull nginx
```

**创建本地挂载目录**

```
mkdir -p /home/nginx/{conf,html,logs}
```


**创建nginx.conf**

```
cat <<EOL | sudo tee /home/nginx/conf/nginx.conf
events { }

http {
    server {
        listen 80;
        server_name localhost;

        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }

        error_log /var/log/nginx/error.log;
        access_log /var/log/nginx/access.log;
    }
}
EOL
```

**创建一个 HTML 文件**

```
echo '<h1>Hello, Nginx from Docker!</h1>' > /home/nginx/html/index.html
```

**运行**

```
docker run -d \
  --name nginx \
  
  -p 80:80 \
  -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf:ro \
  -v /home/nginx/html:/usr/share/nginx/html:ro \
  -v /home/nginx/logs:/var/log/nginx \
  nginx
```