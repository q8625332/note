右键仓库目录：


```
TortoiseGit → Settings

Network / 网络
```

## 修改 SSH Client

```
C:\Windows\System32\OpenSSH\ssh.exe
```

### 确认你的私钥位置

建议把密钥放到：

```
C:\Users\Administrator\.ssh\
```


还需要配置 config 文件

> .ssh 目录下的config文件


```
Host gitlab.域名.cn
    HostName gitlab.域名.cn
    User git
    Port 22
    IdentityFile C:/Users/Administrator/.ssh/id_rsa
    IdentitiesOnly yes
```
