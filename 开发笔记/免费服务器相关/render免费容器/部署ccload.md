首先使用谷歌账号注册 [Render](https://render.com/)，然后选择 **New Web Services**。

![06babc315723f1ca1a66c7dc497e3d4a427cf611.png](https://cdn3.ldstatic.com/original/4X/0/6/b/06babc315723f1ca1a66c7dc497e3d4a427cf611.png)

### 2. 配置 Docker 镜像

填入镜像地址：`ghcr.io/caidaoli/ccload:latest`


### 3. 配置环境变量


```
// 数据库
CCLOAD_MYSQL
ljq1235454456:OFXzxvScW121321QkyjW52@tcp(mysql6.sqlpub.com:3311)/ljqccload?charset=utf8mb4

// 登录密码
CCLOAD_PASS  密码

// 启用混合模式
CCLOAD_ENABLE_SQLITE_REPLICA  1
```
