
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