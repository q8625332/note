## 手动配置jdk和maven

> 第一步：按ctrl + shift + p, 搜索“Preferences: Open User Settings (JSON)” 并回车，打开 `settings.json` 文件 。然后添加或修改以下内容：


```
"java.home": "你的 JDK 安装路径（对应 JAVA_HOME 变量值）",
"java.configuration.maven.userSettings": "你的 Maven 配置文件 settings.xml 所在路径"
```

> 第二步：保存该文件，并重启cursor即可。