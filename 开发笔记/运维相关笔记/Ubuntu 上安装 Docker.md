## 系统更新

```
sudo apt update
```

## 检查系统版本

```
lsb_release -a
```

## 安装 Docker

```
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

### 添加 Docker 的官方 GPG 密钥

```
```text
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```

### 添加 Docker 的 APT 源

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

### 更新 APT 包索引

```
sudo apt update
```

### 安装 Docker CE

```
sudo apt install docker-ce
```

### 验证 Docker 是否安装成功

```
sudo systemctl status docker
```

### 启动 Docker 服务

```
sudo systemctl start docker
```

### 自动启动

```
sudo systemctl enable docker
```