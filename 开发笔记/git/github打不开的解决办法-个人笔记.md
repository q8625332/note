## github最近打不开，很久之前遇到过，但是忘记怎么解决了，查找相关资料后，今天记录在此，以备不时之需。

> 记住3个关键网址
> 
> github网址查询：The world's leading software development platform · GitHub
> 
> github域名查询：▷ github.global.ssl.fastly.net Website statistics and
> traffic analysis
> 
> github静态资源ip：https://github.com.ipaddress.com/assets-cdn.github.com
> 
> github网址ip查询


![在这里插入图片描述](https://img-blog.csdnimg.cn/20210429112951246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
github域名查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/a3ca559d22c4b2c5833b73214b40687c.png#pic_center)
github静态ip查询

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210429113141592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
**win10 cmd 打开**

```java
C:\WINDOWS\system32\drivers\etc
```
**修改host文件，修改host文件，在最后附上下面的内容**

```java
140.82.112.4 github.com
199.232.69.194 github.global.ssl.fastly.net
185.199.108.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
185.199.111.153 assets-cdn.github.com
```
