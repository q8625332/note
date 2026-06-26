
```md
# Windows 安装 RTK（Rust Token Killer）

`rtk-ai/rtk` 是 **Rust Token Killer**。

Windows 推荐安装方式是下载预编译包：

```text
rtk-x86_64-pc-windows-msvc.zip
```

解压后将 `rtk.exe` 放到 `PATH` 中即可。

当前 GitHub 最新 Release：

- 版本：`v0.42.4`
- 发布时间：`2026-06-12`

> 官方特别提醒：不要双击 `rtk.exe`，要在 PowerShell / CMD / Windows Terminal 中运行。

参考来源：

- GitHub README：<https://github.com/rtk-ai/rtk>
- 官方安装文档：<https://www.rtk-ai.app/docs/getting-started/installation/>
- GitHub Release API：<https://api.github.com/repos/rtk-ai/rtk/releases/latest>


## 最简单的是ai 自动化安装


```
读取一下url ： https://github.com/rtk-ai/rtk

看看win电脑上如何安装。

可以，你帮我安装上，并全局配置 codex和claude code.
```


检查是否有使用


```
rtk gain
```


rtk gain 当前显示 No tracking data yet，这是正常的，说明还没有通过 RTK 跑过可统计的命令。

有压缩是这样样子的：

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/086b4af151e04b0c9b91ebf82f3c7d13.png)



---

## Windows 原生安装

使用 PowerShell 执行：

```powershell
$InstallDir = Join-Path $env:USERPROFILE ".local\bin"
$ZipPath = Join-Path $env:TEMP "rtk-x86_64-pc-windows-msvc.zip"

New-Item -ItemType Directory -Force -Path $InstallDir | Out-Null

Invoke-WebRequest `
  -Uri "https://github.com/rtk-ai/rtk/releases/latest/download/rtk-x86_64-pc-windows-msvc.zip" `
  -OutFile $ZipPath

Expand-Archive -Force -Path $ZipPath -DestinationPath $InstallDir

$UserPath = [Environment]::GetEnvironmentVariable("Path", "User")
if (($UserPath -split ';') -notcontains $InstallDir) {
    [Environment]::SetEnvironmentVariable("Path", "$UserPath;$InstallDir", "User")
}

$env:Path += ";$InstallDir"

rtk --version
rtk gain
```

如果 `rtk gain` 能显示 `token savings` 统计，就说明安装正确。

> 注意：有另一个同名 `rtk` 包叫 **Rust Type Kit**。
> 官方建议使用 `rtk gain` 来确认是否安装成了正确项目。

---

## 初始化到 AI 工具

### Codex CLI

如果你使用的是 Codex CLI：

```bash
rtk init --codex
```

全局生效：

```bash
rtk init --global --codex
```

---

### Claude Code

如果你使用 Claude Code：

```bash
rtk init --global
```

不过根据官方 Supported Agents 文档，Windows 原生环境下 shell hook 依赖 Unix shell，因此：

```bash
rtk init -g
```

在 Windows 原生环境中会降级为说明文件注入模式。

这意味着：

```bash
rtk git status
rtk cargo test
```

这类命令仍然可用，但不会像 Linux / macOS 那样自动拦截并重写命令。

如果想获得完整 hook 体验，建议在 **WSL** 中安装并使用 RTK。

---

## Rust / Cargo 安装方式

如果你已经安装了 Rust，也可以使用：

```bash
cargo install --git https://github.com/rtk-ai/rtk rtk
rtk gain
```

不建议直接优先使用：

```bash
cargo install rtk
```

因为可能会安装到同名的错误项目。


安装完可以让ai，帮你安装到工具上面，可以将rtk，帮我安装上，并全局配置 codex和claude code.