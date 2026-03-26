
> 为什么要升级呢。因为5.1编辑文件的时候默认是用utf-8 bom格式的。使用ai时，生成的文件格式会有问题。所有需要换掉5.1改为7

**安装**


```
winget install --id Microsoft.PowerShell --source winget
```

检查版本


```
$PSVersionTable.PSVersion
```

如果版本还是5的：

```
Major  Minor  Build  Revision
-----  -----  -----  --------
5      1      26100  4061
```

就替换一下配置。

先查询


```
Get-ChildItem "C:\Program Files\PowerShell" -Recurse -Filter pwsh.exe -ErrorAction SilentlyContinue | Select-Object -ExpandProperty FullName
```

结果

```
PS C:\Users\Administrator> Get-ChildItem "C:\Program Files\PowerShell" -Recurse -Filter pwsh.exe -ErrorAction SilentlyContinue | Select-Object -ExpandProperty FullName
C:\Program Files\PowerShell\7\pwsh.exe
PS C:\Users\Administrator> & C:\Program Files\PowerShell\7\pwsh.exe
```

启动


```
& C:\Program Files\PowerShell\7\pwsh.exe
```

把 PowerShell 7 路径加到系统 PATH（管理员 PowerShell 执行）


```
[Environment]::SetEnvironmentVariable(
  "Path",
  $env:Path + ";C:\Program Files\PowerShell\7\",
  "Machine"
)
```

查询

```
pwsh
```

## 在 PowerShell 7 中统一默认编码为 UTF-8 无 BOM

打开 profile：

```
if (!(Test-Path $PROFILE)) { New-Item -ItemType File -Path $PROFILE -Force }
notepad $PROFILE
```

加入下面一行并保存：

```
$PSDefaultParameterValues['*:Encoding'] = 'utf8NoBOM'
```

重开 `pwsh` 生效。

## 设置默认终端


![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/264adffd03f149038d2a9a2548719b6b.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/eba082f799ec482bb07d6067eeaa1b53.png)
