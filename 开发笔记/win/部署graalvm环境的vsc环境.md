>用vfox


> 下载 vsc的微软软件
> 然后安装 c++ 编译

![[Pasted image 20241219160102.png]]

> 记住这个地址，等下设置系统变量的时候要使用到

![[Pasted image 20241219160325.png]]

> 配置系统变量

![[Pasted image 20241219160429.png]]

```
VISUAL_STUDIO
D:\VisualStudio\VS\VC\Tools\MSVC\14.42.34433


//第二个

Path 环境变量

%VISUAL_STUDIO%\bin

//第三个

记住 10.0.22621.0 不同版本名字不一样

INCLUDE 环境变量

%VISUAL_STUDIO%\include;
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\shared;
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\ucrt;
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\um;
C:\Program Files (x86)\Windows Kits\10\Include\10.0.22621.0\winrt;

//第四个

记住 10.0.22621.0 不同版本名字不一样

lib 环境变量
%VISUAL_STUDIO%\lib\x64;
C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22621.0\um\x64;
C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22621.0\ucrt\x64;

```

