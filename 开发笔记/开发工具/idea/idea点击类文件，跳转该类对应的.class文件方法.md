> 有时候想通过当前类，来找到编译后的文件相当难找，可以通过这个方法，来快速定位到编译后的文件

[![15582833b52586725715479e1aedc6ca82533.png](https://img.meituan.net/portalweb/15582833b52586725715479e1aedc6ca82533.png)](https://img.meituan.net/portalweb/15582833b52586725715479e1aedc6ca82533.png)

**流程**
1. 第一个program：选择你的idea安装目录，选中idea64.exe文件
2. 第二个arguments：直接复制 `$FileNameWithoutExtension$.class`
3. 第三个working directory：直接复制 `$OutputPath$\$FileDirRelativeToSourcepath$`

> 选择一个类文件，右键，就能找到配置的这个快速跳转。如果你需要配置快捷键，使用idea自带的keymap即可。

[![cec23eca012e13036cf02939bcd68e7492493.png](https://img.meituan.net/portalweb/cec23eca012e13036cf02939bcd68e7492493.png)](https://img.meituan.net/portalweb/cec23eca012e13036cf02939bcd68e7492493.png)
