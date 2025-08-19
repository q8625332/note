## 运行脚本失败，被策略禁止

> 运行以下代码

```java
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

> 查看接口监听

```win
netstat -ano | findstr "8888"
```