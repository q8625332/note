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
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

### 添加 Docker 的官方 GPG 密钥

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### 添加 Docker 的 APT 源

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

