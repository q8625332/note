### 查看内核版本

```
uname -r          # 内核版本号
uname -a          # 完整系统信息
```

### 更新系统包

```
sudo apt update
sudo apt upgrade -y
```

### 安装依赖包

```
sudo apt install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
### 添加 Docker 官方 GPG 密钥

```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
### 添加 Docker 仓库

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 安装后配置

---
### 启动并启用 Docker 服务

```
sudo systemctl start docker
sudo systemctl enable docker
```
### 验证 Docker 安装

```
sudo docker --version
```
