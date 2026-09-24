### 操作
- 可以。Windows 11 默认使用精简右键菜单，可通过注册表让“显示更多选项”直接展开经典菜单。
- 以当前用户身份打开 PowerShell，执行：


```
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
Stop-Process -Name explorer -Force
Start-Process explorer.exe
```
