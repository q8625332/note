## 下载地址

[Apache Tomcat® - Apache Tomcat 9 Software Downloads](https://tomcat.apache.org/download-90.cgi "tomcat")

## Tomcat配置环境变量

![undefined](https://m.360buyimg.com/i/jfs/t1/329740/14/13461/13906/68c7dadcFe4904df5/0a3a06de8e2ef992.png)


```
变量名：CATALINA_HOME
```

**之后找系统变量中的Path，点击编辑：**

![undefined](https://m.360buyimg.com/i/jfs/t1/342353/36/2311/40967/68c7daecFeb77e41a/4906abec77be9ae3.png)


```
%CATALINA_HOME%\bin
```

## **验证Tomcat配置是否成功**

首先win+R输入cmd回车，然后再DOS窗口输入startup.bat回车。

之后会出现Tomcat启动窗口。

![undefined](https://m.360buyimg.com/i/jfs/t1/329276/20/13488/213431/68c7db03F1366b221/2fcb961a98666434.png)
代表配置成功。

如果出现报错或者一闪而过，可能是端口号被占用。Tomcat默认窗口时8080。

可以尝试重启电脑，再启动Tomcat试试。不行的话，就修改Tomcat端口号。

如果配置成功，打开浏览器，输入http://localhost:8080/

（刚才那个黑窗口一定不能关闭，否则肯定打不开这个页面）

如果出现如下图，则表示成功。

![undefined](https://m.360buyimg.com/i/jfs/t1/324040/3/19393/324547/68c7db11F5ac5e62a/ebc97ff211f146c5.png)
