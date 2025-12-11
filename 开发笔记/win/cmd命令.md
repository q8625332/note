## 检查wifi的信息

```cmd
netsh wlan show wirelesscapabilities
```


## 用命令手动刷新桌面组件

> 就是底部的的组件栏


```
// 停止
taskkill /f /im explorer.exe

// 重新启动
start explorer.exe
```


**使用 PowerShell 测试端口连接**


```
Test-NetConnection rocketmq.gfw.com -Port 9876

curl -v telnet://rocketmq.gfw.com:9876
```
