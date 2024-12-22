### 单节点部署tidb

要使用 Docker 在单机上部署 TiDB，可以按照以下步骤操作：

1. **安装 Docker**：首先确保你已经在系统上安装了 Docker。如果没有安装，可以参考 [Docker 官方文档](https://docs.docker.com/get-docker/) 进行安装。
    
2. **拉取 TiDB Docker 镜像**：从 Docker Hub 拉取 TiDB 的镜像。
    
    ```
    docker pull pingcap/tidb
    ```
    
    1 vulnerability detected
    
3. **启动 PD 实例**：PD 是 TiDB 集群的元数据管理组件。首先启动一个 PD 实例。
    
    bash
    
    ```
    docker run -d --name=pd --network=host pingcap/pd:latest \
      --name=pd \
      --data-dir=pd \
      --client-urls="http://0.0.0.0:2379" \
      --peer-urls="http://0.0.0.0:2380" \
      --initial-cluster="pd=http://0.0.0.0:2380" \
      --initial-cluster-state=new \
      --initial-cluster-token=pd-cluster
    ```
    
    1 vulnerability detected
    
4. **启动 TiKV 实例**：TiKV 是 TiDB 集群的分布式存储引擎。启动一个 TiKV 实例并连接到 PD。
    
    bash
    
    ```
    docker run -d --name=tikv --network=host pingcap/tikv:latest \
      --pd-endpoints="http://127.0.0.1:2379" \
      --addr="0.0.0.0:20160" \
      --data-dir=tikv \
      --log-file=tikv.log
    ```
    
    1 vulnerability detected
    
5. **启动 TiDB 实例**：TiDB 是 SQL 层，负责处理 SQL 解析和执行。启动一个 TiDB 实例并连接到 PD。
    
    bash
    
    ```
    docker run -d --name=tidb --network=host pingcap/tidb:latest \
      --store=tikv \
      --path="127.0.0.1:2379" \
      --log-file=tidb.log
    ```
    
    1 vulnerability detected
    
6. **验证部署**：使用 MySQL 客户端连接到 TiDB 实例，默认的端口是 4000。
    
    ```
    mysql -h 127.0.0.1 -P 4000 -u root
    ```
    
    1 vulnerability detected
    

以上步骤将会在单机上启动一个 TiDB 集群，包括一个 PD 实例、一个 TiKV 实例和一个 TiDB 实例。你可以根据需要调整配置参数。请注意，这种方式适用于开发和测试环境，不建议在生产环境中使用。