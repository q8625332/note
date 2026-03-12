**首先看运行的进程号**：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c4cdd9bd26e943d68215c2083a98c763.png)

**启动art** 


```
java -jar arthas-boot.jar
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fcba988922624ab688d8b13e4e094521.png)

找到指定的进程

输入   29

进入： 

```
trace com.ljq.httpserver.filter.PortalRequiresPermissionsValidator checkRequiresButtonPermImpl
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/aa5ddd73c1dd4e218e4ecdb1aa7eba1f.png)

