> 有时候想通过当前类，来找到编译后的文件相当难找，可以通过这个方法，来快速定位到编译后的文件

![[Pasted image 20250630151054.png]]

1. 第一个program：选择你的idea安装目录，选中idea64.exe文件
2. 第二个arguments：直接复制$FileNameWithoutExtension$.class
3. 第三个working directory：直接复制$OutputPath$\$FileDirRelativeToSourcepath$