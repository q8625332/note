**安装 wsl2**

WSL2 需要较新的 Windows 版本。

- **Windows 10**: 版本 2004 或更高 (内部版本 19041 及更高)。
- **Windows 11**: 所有版本都支持。

命令：

**`wsl --install`**

安装完：设置wsl2

```text
wsl --set-default-version 2
```

WSL2 需要 CPU 的硬件虚拟化支持。绝大多数现代电脑都支持，但有时默认是关闭的。

**如何检查？**

- 按下 `Ctrl` + `Shift` + `Esc` 打开“任务管理器”。
- 切换到“性能”选项卡。
- 点击“CPU”。
- 在右下角找到“虚拟化”一项，确保它显示为 **“已启用”**。

完成上面的步骤就可以安装WSL2了。

**重启电脑** 命令执行完毕后，会提示你需要重启电脑以完成安装。请保存好你的工作，然后重启。


重启后，安装过程会自动继续。

1. **自动启动 Ubuntu**
    
    - 系统重启后，一个 Ubuntu 的命令行窗口可能会自动弹出，并显示 "Installing, this may take a few minutes..."。
    - 如果它没有自动弹出，你可以手动点击“开始”菜单，找到新安装的“Ubuntu”程序并启动它。
2. **创建 Linux 用户**
    
    - 等待安装完成后，系统会提示你创建一个新的 UNIX 用户。
    - 输入你想要的 **用户名** (username)，然后按回车。
    - 输入并确认你的 **密码** (password)。输入密码时屏幕上不会显示任何字符，这是 Linux 的正常安全机制，你只管输入然后回车即可。
    - **非常重要**：这个用户名和密码是你在这个 Linux 环境下的管理员（sudoer）账户，它与你的 Windows 账户没有任何关系。请牢记它。
3. **更新你的 Linux 系统 (推荐)** 创建完用户后，你就进入了 Ubuntu 的命令行。作为最佳实践，建议立即更新一下软件包列表和已安装的软件：
    
     

```bash
sudo apt update && sudo apt upgrade
```

    
    系统会要求你输入刚才设置的密码来授权 `sudo` 命令。
    

**恭喜！至此，你已经成功在 Windows 上安装并配置好了 WSL2 和 Ubuntu！** 🚀

### 后续和开发技巧

为了更好的开发体验，强烈推荐以下几步：

1. **安装 Windows Terminal**
    
    - 从 Microsoft Store 搜索并安装 **"Windows Terminal"**。
    - 它是一个现代化的、支持多标签页的终端工具，可以同时管理 PowerShell, CMD, 和多个不同的 WSL Linux 发行版，体验远超原生命令行。
2. **VS Code 与 WSL 无缝集成**
    
    - 在 Windows 上安装 Visual Studio Code。
    - 打开 VS Code，在扩展商店中搜索并安装 **"Remote - WSL"** 扩展。
    - 安装后，点击 VS Code 左下角的绿色图标，选择 "Connect to WSL"，你就可以直接在 Windows 的 VS Code 界面中，编写和运行位于 Linux 子系统中的代码了！这是 WSL 最强大的功能之一。
3. **文件系统互访**
    
    - **在 Windows 中访问 Linux 文件**: 打开文件资源管理器，在地址栏输入 `\\wsl$` 并回车，你就能看到所有已安装的 Linux 发行版的文件系统。
    - **在 Linux 中访问 Windows 文件**: 你的 Windows 盘符会自动挂载到 Linux 的 `/mnt/` 目录下。例如，你的 C 盘就是 `/mnt/c`，D 盘就是 `/mnt/d`。

现在，你就可以在 WSL2 的 Ubuntu 环境中安装 GraalVM、Maven/Gradle、Java 等工具，享受原生 Linux 般的流畅开发体验了！


## 错误背景分析

> # 解决 WSL 安装报错误代码: 错误代码: Wsl/InstallDistro/Service/RegisterDistro/CreateVm/HCS/HCS_E_SERVICE_NOT_AVAILABLE


**当在 Windows 系统执行 wsl --install 安装 Linux 的 Windows 子系统 (WSL) 时，可能遭遇以下关键错误：**


```bash
错误代码: Wsl/InstallDistro/Service/RegisterDistro/CreateVm/HCS/HCS_E_SERVICE_NOT_AVAILABLE
```


该错误通常与 虚拟机平台 功能未正确启用或系统版本过低有关，本文将彻底解决该问题。

## 第一阶段：基础功能验证

### 步骤 1：启用核心组件

1.按下 Win 键，搜索 appwiz.cpl 回车  
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/716f97f892e4410482f5b83b945dab48.png)

2.选择 启用或关闭 Windows 功能  
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/87ffe65f4ec04606b8276bde1228715d.png)

3.勾选以下两项（必须重启）：  
✅ 虚拟机平台  
✅ 适用于 Linux 的 Windows 子系统  
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/10fc97a107bf459ebf713065b64dfa0f.png)

### 步骤 2：强制启用虚拟化功能

在管理员模式下打开 PowerShell 或 Windows 命令提示符


```bash
dism.exe /Online /Enable-Feature /FeatureName:VirtualMachinePlatform /All
```

 完结

## 切换root


```bash
sudo -s
```


## 进入对应的win目录


```bash
# 在 WSL2 终端中，你可以通过 `/mnt/c/` 访问 C 盘。
# 例子：
cp /mnt/c/Users/YourUser/Downloads/graalvm-demo.zip
```

**小技巧**：你可以直接在 Windows 文件资源管理器中，将 ZIP 文件拖拽到 VS Code (已连接到 WSL) 的文件浏览器中，然后右键解压，更直观。