## 手动配置jdk和maven

> 第一步：按ctrl + shift + p, 搜索“Preferences: Open User Settings (JSON)” 并回车，打开 `settings.json` 文件 。然后添加或修改以下内容：


```
"java.jdt.ls.java.home": "你的 JDK 安装路径（对应 JAVA_HOME 变量值）"

"maven.settingsFile": "C:\\Users\\Administrator\\.m2\\conf\\dan_xiao_settings.xml"

//maven、gradle的配置文件变更后自动更新 
"java.configuration.updateBuildConfiguration": "automatic",

```

> 第二步：保存该文件，并重启cursor即可。

# Cursor 修改布局

> 使用vscode布局

快捷键： `Ctrl + ,`  打开设置页面

搜索框输入： `workbench.activityBar.orientation`

根据图片 选择 `vertical`

最后根据提示重启 `Cursor` 即可恢复同 `VS Code` 相同布局

## 快捷键配置

[![ffb1550c83c5492cb84cd16a5235210215899.png](https://img.meituan.net/portalweb/ffb1550c83c5492cb84cd16a5235210215899.png)](https://img.meituan.net/portalweb/ffb1550c83c5492cb84cd16a5235210215899.png)

## mybatis jump to mapper xml

mybatis跳转xml