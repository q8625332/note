## 安装

> 运行命令

```win
win下安装

winget install vfox
```


```linux
linux下安装

curl -sSL https://raw.githubusercontent.com/version-fox/vfox/main/install.sh | bash

第一步：添加 vfox 钩子到你的 .bashrc 文件

echo 'eval "$(vfox activate bash)"' >> ~/.bashrc

在当前终端里执行 `source ~/.bashrc` 来立即加载新的配置。

source ~/.bashrc

```


[官网 | vfox](https://vfox.dev/zh-hans/guides/quick-start.html)

```
# add plugin for vfox
vfox add java

# add runtime 
vfox install java@x.y.z  # Use openjdk as default distribution.
vfox install java@x.y.z-graal # Use graalvm as distribution.

# view all available versions
vfox search java all # view all java sdks
vfox search java # view all for openjdk
vfox search java graal # view all for graalvm

vfox use 

```

## maven的小技巧


```
// 安装maven
vfox install maven

// 查询jdk和maven的安装目录
mvn -v

Apache Maven 3.9.9 (8e8579a9e76f7d015ee5ec7bfcdc97d260186937)
Maven home: C:\Users\Administrator\.version-fox\cache\maven\current
Java version: 1.8.0_342, vendor: Oracle Corporation, runtime: C:\Users\Administrator\.version-fox\cache\java\current\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```
