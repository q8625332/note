> 一个rust写的轻量级nacos，相对于阿里的轻很多

[r-nacos官网地址](https://r-nacos.github.io/docs/)

**拉取**

```
# stable 是最新正式版本号，也可以指定镜像版本号，如： qingpan/rnacos:v0.4.0
docker pull qingpan/rnacos:stable
```

**启动**

```
docker run -d \
	--name nacos \
	-p 8848:8848 -p 9848:9848 -p 10848:10848 \
	qingpan/rnacos:stable
```