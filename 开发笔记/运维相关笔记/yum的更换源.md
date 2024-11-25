>项目的yum源旧了。导致yum下载不了新的数据，就需要更新源

*更换使用阿里的源*

#### yum源配置文件路径：

```
/etc/yum.repos.d/CentOS-Base.repo
```

#### 查看CentOS-Base.repo文件内容：

```
cat /etc/yum.repos.d/CentOS-Base.repo
```

#### 备份官方源配置：

```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```

#### 下载阿里云yum源配置文件覆盖原官方源配置文件：

```
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```