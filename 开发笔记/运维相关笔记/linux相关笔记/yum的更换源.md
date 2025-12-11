>项目的yum源旧了。导致yum下载不了新的数据，就需要更新源

*更换使用阿里的源*

#### yum源配置文件路径：

```linux
/etc/yum.repos.d/CentOS-Base.repo
```

#### 查看CentOS-Base.repo文件内容：

```linux
cat /etc/yum.repos.d/CentOS-Base.repo
```

#### 备份官方源配置：

```linux
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```

#### 下载阿里云yum源配置文件覆盖原官方源配置文件：

```linux
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7f60ce03f3ba453ba578f1ff91e7f1b6.png)

#### 清理缓存并生成新的缓存：

```linux
yum clean all

yum makecache
```

#### 更新软件

```linux
yum -y update
```
