
**分析工具**


```
MemoryAnalyzer
```

先加载dump文件

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/000703a182e64ef2babe497026e373ab.png)

**如果是oom**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/35e2fb9eb43448fea814d620eda071f9.png)

这个值会偏大，30%算是正常范围。

**看大对象**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8c091955260d4550869dbc3139e7dc0d.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3f140989c7c44b3a885bc0925ebe7e8f.png)

Shallow Heap 代表是本身的大小。
Retained Heap 本身大小加上应用的大小。

**看具体报错：**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3ffb802b3f644247986c3fa0ce63ec23.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/30994f164bea4398bad9cf30cc6518ec.png)

这个报错就是线程报错了。就是线程不够用了。

**如果想要看见大对象里的具体报错内容**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f956539500a74bfd879da16acf7c028a.png)


![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9d28d665dc8e4142ad337734c59ce668.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d6233390a760416e82e09323688d3ccb.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/83e0f9c20d3241d4913aff1536ea7a0a.png)

这样就能看出是是string的数组影响的。