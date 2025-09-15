## 运行项目出现无符号问题
1. 添加jvm的配置（也可以解决java: 找不到符号 问题）

```java
-Djps.track.ap.dependencies=false
```

**贴图：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/cf044338843b485690830c3ea427f2d5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
2. 如果是编码问题的话
![在这里插入图片描述](https://img-blog.csdnimg.cn/9791e708be24462b95d05738ebe14594.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
## 忽略大小写

![在这里插入图片描述](https://img-blog.csdnimg.cn/558fbd256c5e42cfa2bd4a4c2be467d9.png)
## 代码太长，自动换行
![在这里插入图片描述](https://img-blog.csdnimg.cn/32b2760254344c27be388ed0d52572a3.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/6088974beb9a48fc8fd0c5d57940d2a3.png)
## 编译出现问题

>如果出现这种报错： `javac <options> <source files>` 是因为编译的文件编码不一致导致的。

**解决办法**

```java
-DarchetypeCatalog=internal -Dfile.encoding=UTF-8
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/af0855050afc4b7d942125af0dc8d1e3.png)
这样就解决了

## sql使用#参数的设置

> 英文的搜这个

```java
User Parameters
```

> 中文的看如下图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/3029a080e923406da5a57621c76be760.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/440b9d58526d411fab2ef625550e8845.png)

> 代码

```java
#\{([^\{\}]*)\}
```

> 使用效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/0723d8ba9e56481d8d6662c2ced8b619.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/50e1d47bf70a49a6a2cc5f63fc18a4cf.png)

## idea磁盘满了，解决方法

> 打开自定义设置

[![19b0efac712dd003f2865ebe337264282298.png](https://img.meituan.net/portalweb/19b0efac712dd003f2865ebe337264282298.png)](https://img.meituan.net/portalweb/19b0efac712dd003f2865ebe337264282298.png)

> 对应的磁盘下建立

*目录下建立以`.IntelliJIdea`*

[![990e44d49120d188f45d18f46af0eb3788261.png](https://img.meituan.net/portalweb/990e44d49120d188f45d18f46af0eb3788261.png)](https://img.meituan.net/portalweb/990e44d49120d188f45d18f46af0eb3788261.png)

```java
idea.config.path=D:/.IntelliJIdea/config  
idea.system.path=D:/.IntelliJIdea/system
```

## 按键冲突了的解决办法

[![785e832ffa589763a902757c3dea63ec80445.png](https://img.meituan.net/portalweb/785e832ffa589763a902757c3dea63ec80445.png)](https://img.meituan.net/portalweb/785e832ffa589763a902757c3dea63ec80445.png)

## 行注释：

> [!前言]
> 在idea的初始化设置中，使用注释快捷键，会出现如下的情况，`//`注释符出现在行首，显得整体页面很乱

[![1cca0671bbb4da63283fab601988b72315004.png](https://img.meituan.net/portalweb/1cca0671bbb4da63283fab601988b72315004.png)](https://img.meituan.net/portalweb/1cca0671bbb4da63283fab601988b72315004.png)

**想要的注释是（清晰美观）：**

[![810eb61de8846716d9652321302f315511501.png](https://img.meituan.net/portalweb/810eb61de8846716d9652321302f315511501.png)](https://img.meituan.net/portalweb/810eb61de8846716d9652321302f315511501.png)

去掉红色的☑️

[![8f6cc34b077d9339fb1ccc2e2b6afbcc64643.png](https://img.meituan.net/portalweb/8f6cc34b077d9339fb1ccc2e2b6afbcc64643.png)](https://img.meituan.net/portalweb/8f6cc34b077d9339fb1ccc2e2b6afbcc64643.png)

效果就如上。