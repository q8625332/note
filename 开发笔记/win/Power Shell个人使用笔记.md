## 运行脚本失败，被策略禁止

> 运行以下代码

```java
get-ExecutionPolicy
Set-ExecutionPolicy -Scope CurrentUser
RemoteSigned
```

> 查看接口监听

```win
netstat -ano | findstr "8888"
```