idea安装smart tomcat

> 配置描述

配置执行前，需要配置构建指定的文件，不要配置构建全部，不然多项目的时候很麻烦。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/aa4355486a844355aa34a48533c09117.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/4cdbe984ee004ce18d587b00f1d26eb3.png)

```
// 如果log4j 因为中文名目录报错了，就将文件放到英文目录，用jvm参数加载

-Dlog4j.configuration=file:///D:/work/app-remote-tomcat-log4j/log4j.xml
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0dcd9fe606134ec0a452e7d86600b07c.png)


