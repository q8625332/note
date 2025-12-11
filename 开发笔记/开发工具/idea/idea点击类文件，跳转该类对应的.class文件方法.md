> 有时候想通过当前类，来找到编译后的文件相当难找，可以通过这个方法，来快速定位到编译后的文件

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0561f71caa7b4f749f7e8503d098d82e.png)

**流程**
1. 第一个program：选择你的idea安装目录，选中idea64.exe文件
2. 第二个arguments：直接复制 `$FileNameWithoutExtension$.class`
3. 第三个working directory：直接复制 `$OutputPath$\$FileDirRelativeToSourcepath$`

> 选择一个类文件，右键，就能找到配置的这个快速跳转。如果你需要配置快捷键，使用idea自带的keymap即可。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0b47a3b8c00048c9b7f3c869db2c8315.png)
