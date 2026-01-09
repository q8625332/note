> 有黑客会在比如论坛的网站上面增加自己盗用别人信息的链接。
> 需要预防这种情况只允许访问白名单里面的资源，就需要用到csp这样的功能。

在nginx的头里面增加csp的配置。

例子：

> add_header content-securrity-policy 这个就是添加csp的头。

```
location ^~ /admin-test {

	try_files $uri $uri/ /admin-test/index.html;
	
	include agent_deny.conf;
	
	alias /bond-admin;
	
	index index.html;
	
	add_header Cache-Control "no-cache,public,max-age=300";
	
	add_header Access-Control-Allow-Origin "*";
	
	add_header content-securrity-policy "default-src 'self' *.域名.com; script-src 'self' *.域名.com [https://unpkg.com](https://unpkg.com) 'unsafe-inline' 'unsafe-eval'; style-src 'self' *.域名.com [https://unpkg.com](https://unpkg.com) 'unsafe-inline'; img-src 'self' *.域名.com data:; font-src 'self' *.域名.com data: at.alicdn.com; connect-src *; media-src 'self' *.域名.com";

}
```
