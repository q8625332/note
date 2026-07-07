
如果你想继续使用 TortoiseGitPlink，那么需要把 OpenSSH 私钥转换成 PuTTY 的 `.ppk` 格式。

## 1. 打开 PuTTYgen

一般路径类似：


```
C:\Program Files\TortoiseGit\bin\puttygen.exe
```

或者开始菜单搜索：

```
PuTTYgen
```

## 2. 导入私钥

点击：

```
Conversions → Import key
```


选择你的私钥：


```
dx-ljq-key
```

注意不是 `.pub` 文件。

## 保存为 ppk

点击：

```
Save private key
```

保存为：

```
dx-ljq-key.ppk
```

## 在 TortoiseGit 里指定私钥

右键仓库目录：


```
TortoiseGit → Settings → Git → Remote
```

找到你的远端 `ljq-note`，下面应该有一个类似：

```
Putty Key
```

选择：

```
dx-ljq-key.ppk
```


然后确定。