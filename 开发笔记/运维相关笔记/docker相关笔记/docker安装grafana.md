> 安装grafana
> Grafana 9.0.1 对应的promtail和loki是2.9.1版本。需要配置对应版本

**暂时未写**

## 安装promtail
> docker安装


```linux
# 拉取 Promtail 镜像
docker pull grafana/promtail:2.9.1
```


> /path/to/promtail-config.yaml 是系统路径的配置文件
> scrape_configs 是爬取的任务。
> 任务名字（job_name）：persistence-service。
> 文件路径：`__path__`,docker镜像的，需要访问系统文件需要挂载带容器中

**/path/to/promtail-config.yaml**

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://192.168.1.99:3100/loki/api/v1/push

scrape_configs:
  - job_name: persistence-service
    static_configs:
      - targets:
          - localhost
        labels:
          job: persistence-logs
          __path__: /hytto/deploy/gfw/rpc_services/logs/persistance-service/stdout.log
```

**运行代码**

```linux
docker run -d --name=promtail \
 -v /path/to/promtail-config.yaml:/etc/promtail/config.yml \
 -v /hytto/deploy/gfw/rpc_services/logs/:/hytto/deploy/gfw/rpc_services/logs/ \
 -p 9080:9080 \
 grafana/promtail:2.6.1 \
 -config.file=/etc/promtail/config.yml
```

## 安装loki
> 安装loki

```linux
# 拉取 Loki 单二进制镜像
docker pull grafana/loki:2.9.1
```

**配置文件**


```yaml
auth_enabled: false
server:
  http_listen_port: 3100

ingester:
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
    final_sleep: 0s
  chunk_idle_period: 1h
  max_chunk_age: 1h
  chunk_target_size: 1536000
  chunk_retain_period: 30s

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h

storage_config:
  boltdb_shipper:
    active_index_directory: /data/loki/index
    cache_location: /data/loki/cache
    cache_ttl: 24h
    shared_store: filesystem
  filesystem:
    directory: /data/loki/chunks

# compactor 配置（在 2.6.1 中用于 boltdb-shipper 的清理）
compactor:
  working_directory: /data/loki/compactor
  shared_store: filesystem
  compaction_interval: 10m
  retention_enabled: true        # 启用保留策略
  retention_delete_delay: 2h     # 删除延迟
  retention_delete_worker_count: 150

# 全局限制配置
limits_config:
  reject_old_samples: true
  reject_old_samples_max_age: 168h
  retention_period: 24h          # 全局保留期 24 小时

chunk_store_config:
  max_look_back_period: 0s

# table_manager 在 2.6.1 中仍然存在，但对 boltdb-shipper 作用有限
table_manager:
  retention_deletes_enabled: false  # 对 boltdb-shipper 无效，设为 false
  retention_period: 0s              # 不使用 table_manager 的保留策略
```


**运行代码**

```linux
# 运行 Loki（单机模式）
docker run -d --name=loki -p 3100:3100 grafana/loki:2.6.1

#挂载运行
mkdir -p /path/to/loki-data/{chunks,index}
chmod -R 777 /path/to/loki-data  # 开放权限（或调整为更严格的属主）

# -v $(pwd)/loki-config.yaml:/etc/loki/loki.yaml`：挂载自定义配置文件。
# -v /path/to/loki-data:/data/loki`：持久化存储（避免容器重启后数据丢失）。
# -v /path/to/loki-data:/data/loki \  # 持久化存储（可选）

docker run -d --name=loki -p 3100:3100 \
  --user=root \
  -v /path/to/loki-config.yaml:/etc/loki/loki.yaml \
  -v /path/to/loki-data/:/data/loki/ \
  grafana/loki:2.6.1 \
  -config.file=/etc/loki/loki.yaml
```

查询loki文件da

## grafana配置loki

![[Pasted image 20250609142224.png]]
![[Pasted image 20250609142858.png]]
