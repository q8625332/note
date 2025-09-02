>用vfox安装java环境后

安装graal


```bash
vfox search java graal
```

安装maven

[[vfox 环境版本管理工具]]

> 废弃使用原生的win开发环境c++来编译。
> 因为过于笨重和庞大，复杂度太高。
> 所以使用wsl环境，直接在win下运行linux系统的内核来实现c++的编译。


安装步骤参考：

[[安装 wsl2]]


## ubuntu 安装环境

**安装vfox**

[[vfox 环境版本管理工具]]

**安装 graalvm**

跳过

**安装 maven**

跳过

GraalVM Native Image 需要本地的 C/C++ 编译器和库来链接最终的可执行文件。在 Ubuntu 上，这非常简单。


```bash
sudo apt update
sudo apt install curl zip unzip
sudo apt install build-essential libz-dev zlib1g-dev
```

- `build-essential` 是一个元数据包，它会安装 GCC 编译器、make 等一系列构建 C/C++ 程序所需的基础工具。
- `zlib1g-dev` 是 GraalVM 明确要求的依赖库。

