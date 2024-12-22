### 单节点部署tidb

**单机部署tidb**

```
docker pull xuxuclassmate/tidb

docker run --name tidb -d --privileged=true -p 4000:4000 xuxuclassmate/tidb
```

进入容器

```
mysql -h 127.0.0.1 -P 4000 --prompt="tidb> "


#创建数据库

CREATE DATABASE demo CHARACTER;
#创建数据库登录用户

CREATE USER 'test'@'%' IDENTIFIED BY 'test';
GRANT ALL PRIVILEGES ON demo.* TO 'test'@'%';
FLUSH PRIVILEGES;
# 更新登录密码

use mysql

update user set authentication_string = password('123456') where User = 'root';

FLUSH PRIVILEGES;

退出数据库，再次登录（使用设置的账号和密码）

mysql -h 127.0.0.1 -P 4000 -u root -p --prompt="tidb> "
```

### 那以后单节点需要变成多节点，需要怎么操作？

将 TiDB 从单节点部署扩展到多节点部署需要以下几个步骤：

1. **准备多台机器**：确保你有多台机器，每台机器都可以通过网络互相访问。每台机器上都要安装 Docker。
    
2. **更新配置**：修改 PD、TiKV 和 TiDB 的配置文件，使它们能够识别新的节点。
    
3. **启动新节点**：在新的机器上启动 PD、TiKV 和 TiDB 实例，并将它们加入到现有的集群中。
    

下面是详细的步骤：

### 1. 安装 Docker（如果尚未安装）

在新的机器上安装 Docker。

```
curl -fsSL https://get.docker.com | sh
```

### 2. 启动新的 PD 实例

在新机器上启动 PD 实例：

bash

```
docker run -d --name=pd --network=host pingcap/pd:latest \
  --name=pd2 \
  --data-dir=pd \
  --client-urls="http://0.0.0.0:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --join="http://<existing_pd_ip>:2379"
```

### 3. 启动新的 TiKV 实例

在新机器上启动 TiKV 实例：

bash

```
docker run -d --name=tikv --network=host pingcap/tikv:latest \
  --pd-endpoints="http://<existing_pd_ip>:2379" \
  --addr="0.0.0.0:20160" \
  --data-dir=tikv \
  --log-file=tikv.log
```

### 4. 启动新的 TiDB 实例

在新机器上启动 TiDB 实例：

bash

```
docker run -d --name=tidb --network=host pingcap/tidb:latest \
  --store=tikv \
  --path="http://<existing_pd_ip>:2379" \
  --log-file=tidb.log
```

### 5. 验证集群状态

在任意一台机器上运行以下命令，检查新的节点是否成功加入集群。

```
docker exec -it pd curl http://127.0.0.1:2379/pd/api/v1/members
```

### 注意事项

- **网络配置**：确保所有节点之间的网络连通性良好。
- **数据一致性**：在扩展期间，确保现有的数据保持一致性，避免数据丢失。
- **负载均衡**：根据业务需求，合理分配节点的角色和负载。

通过以上步骤，你可以将一个单节点的 TiDB 部署扩展成多节点的分布式集群。这样可以提升系统的高可用性和性能。