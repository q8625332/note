**按 Win + X 键选择 Windows PowerShell (管理员)**

注：Win键就是键盘左下角CTRL键右边那个键

**按右键粘贴这行代码后回车即可：**


```
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsUpdate\UX\Settings" /v FlightSettingsMaxPauseDays /t reg_dword /d 10000 /f
```

![[Pasted image 20250721093042.png]]

之后你就可以在 Windows 更新设置——高级选项里面，选择暂停到10000天后更新了：

![[Pasted image 20250721093110.png]]

如果想恢复，可以随时在这里点击“继续更新”。  
  
相较于使用软件，这个方法更原生、更简单。  
  
Win8或更老的系统无法使用这个方法，建议用软件禁用：[Winaero Tweaker](https://yaohuo.me/bbs-755790.html)