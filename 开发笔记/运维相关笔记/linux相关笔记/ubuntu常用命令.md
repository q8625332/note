
## 解压

`7z` 命令用于解压 `.7z` 文件。你可能需要先安装 `p7zip-full` 工具：

- 安装 `p7zip-full`：
    
    ```
    sudo apt-get install p7zip-full
    ```
    
- 解压 `.7z` 文件：
    
    ```
    7z x archive.7z
    ```

### `unzip` 命令

`unzip` 命令用于解压 `.zip` 文件。

- 解压 `.zip` 文件：
    
    ```
    unzip archive.zip
    ```

### `tar` 命令

`tar` 命令可以处理 `.tar`, `.tar.gz`, `.tgz`, `.tar.bz2`, `.tar.xz` 等格式。

- 解压 `.tar` 文件：
    
    ```
    tar -xvf archive.tar
    ```
    
- 解压 `.tar.gz` 或 `.tgz` 文件：
    
    bash
    
    ```
    tar -xvzf archive.tar.gz
    # 或
    tar -xvzf archive.tgz
    ```
    
- 解压 `.tar.bz2` 文件：
    
    ```
    tar -xvjf archive.tar.bz2
    ```
    
- 解压 `.tar.xz` 文件：
    
    ```
    tar -xvJf archive.tar.xz
    ```