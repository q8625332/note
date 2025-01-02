## 部署一个网站

>注意事项：
>第一，要给文件赋予读写权限（755）
>第二，请注意我们使用了 `alias` 指令而不是 `root` 指令。 `alias` 指令会将 `location` 中的路径替换为指定的路径。例子项目用的是`alias` 指令。
>第三，types是可以单独一个文件处理的 `mime.types`。但是我为了方便就写在了一起。


```nginx
events { }

http {

    types {
        text/html                             html htm shtml;
        text/css                              css;
        text/xml                              xml;
        image/gif                             gif;
        image/jpeg                            jpeg jpg;
        application/javascript                js;
        application/atom+xml                  atom;
        application/rss+xml                   rss;

        # Add additional MIME types here if needed
        application/javascript                js;
        application/json                      json;
        application/ld+json                   jsonld;
        application/xml                       xml;
        text/plain                            txt;
        # etc.
    }

    default_type  application/octet-stream;

    server {
        listen 80;
        server_name _;

        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }

        location /base.web/ {
            alias /usr/share/nginx/html/base.web/;
            index index.html index.htm;
        }

        error_log /var/log/nginx/error.log;
        access_log /var/log/nginx/access.log;
    }
}
```

**指定配置域名**

```nginx
events { }

http {

    types {
        text/html                             html htm shtml;
        text/css                              css;
        text/xml                              xml;
        image/gif                             gif;
        image/jpeg                            jpeg jpg;
        application/javascript                js;
        application/atom+xml                  atom;
        application/rss+xml                   rss;

        # Add additional MIME types here if needed
        application/javascript                js;
        application/json                      json;
        application/ld+json                   jsonld;
        application/xml                       xml;
        text/plain                            txt;
        # etc.
    }

    default_type  application/octet-stream;

    server {
        listen 80;
        server_name liujunqin.us.kg www.liujunqin.us.kg;

        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }

        error_log /var/log/nginx/error.log;
        access_log /var/log/nginx/access.log;
    }

    server {
        listen 80;
        server_name base.web.liujunqin.us.kg;

        location / {
            alias /usr/share/nginx/html/base.web/;
            index index.html index.htm;
        }

        error_log /var/log/nginx/base_web_error.log;
        access_log /var/log/nginx/base_web_access.log;
    }
}
```

**添加**