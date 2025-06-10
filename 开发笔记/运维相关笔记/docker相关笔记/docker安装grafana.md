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

**查询loki文件大小，检测sh**


```sh
#!/bin/bash

echo "=== Loki 2.6.1 数据保留功能验证 ==="

LOKI_CONTAINER_NAME="loki"
LOKI_DATA_PATH="/path/to/loki-data"

# 1. 验证配置文件内容
echo "1. 验证配置文件中的关键设置..."
echo "retention_enabled 配置:"
docker exec $LOKI_CONTAINER_NAME grep -A5 -B5 "retention_enabled" /etc/loki/loki.yaml

echo "retention_period 配置:"
docker exec $LOKI_CONTAINER_NAME grep -A5 -B5 "retention_period" /etc/loki/loki.yaml

# 2. 检查 compactor 启动日志
echo ""
echo "2. 检查 compactor 启动和 retention 相关日志..."
echo "=== Compactor 启动日志 ==="
docker logs $LOKI_CONTAINER_NAME 2>&1 | grep -i "compactor\|retention" | tail -10

# 3. 检查是否有 retention 处理日志
echo ""
echo "=== 查找 retention 处理日志 ==="
docker logs $LOKI_CONTAINER_NAME 2>&1 | grep -i "retention.*delete\|retention.*process\|retention.*apply" | tail -5

# 4. 检查数据目录的文件时间戳
echo ""
echo "3. 检查数据文件的时间戳分布..."
echo "最新的 chunk 文件:"
find $LOKI_DATA_PATH/chunks -type f -name "*.gz" -exec ls -lt {} \; | head -5

echo "最旧的 chunk 文件:"
find $LOKI_DATA_PATH/chunks -type f -name "*.gz" -exec ls -lt {} \; | tail -5

# 5. 计算文件年龄
echo ""
echo "4. 分析文件年龄分布..."
echo "超过 24 小时的文件数量:"
find $LOKI_DATA_PATH/chunks -type f -name "*.gz" -mtime +1 | wc -l

echo "最近 24 小时内的文件数量:"
find $LOKI_DATA_PATH/chunks -type f -name "*.gz" -mtime -1 | wc -l

# 6. 检查 compactor 的工作目录
echo ""
echo "5. 检查 compactor 工作状态..."
if [ -d "$LOKI_DATA_PATH/compactor" ]; then
    echo "Compactor 工作目录内容:"
    ls -la $LOKI_DATA_PATH/compactor/
else
    echo "Compactor 工作目录不存在"
fi

# 7. 测试实际的查询API以确认数据范围
echo ""
echo "6. 测试数据时间范围..."

# 查询最近 6 小时的数据
START_6H=$(date -d '6 hours ago' '+%s')000000000
END_NOW=$(date '+%s')000000000
RESULT_6H=$(curl -G -s "http://localhost:3100/loki/api/v1/query_range" \
  --data-urlencode "query={}" \
  --data-urlencode "start=${START_6H}" \
  --data-urlencode "end=${END_NOW}" \
  --data-urlencode "limit=1" | jq -r '.data.result | length' 2>/dev/null || echo "0")

# 查询 30 小时前的数据
START_30H=$(date -d '30 hours ago' '+%s')000000000
END_25H=$(date -d '25 hours ago' '+%s')000000000
RESULT_30H=$(curl -G -s "http://localhost:3100/loki/api/v1/query_range" \
  --data-urlencode "query={}" \
  --data-urlencode "start=${START_30H}" \
  --data-urlencode "end=${END_25H}" \
  --data-urlencode "limit=1" | jq -r '.data.result | length' 2>/dev/null || echo "0")

echo "最近 6 小时有数据: $RESULT_6H 条记录"
echo "30 小时前的数据: $RESULT_30H 条记录"

if [ "$RESULT_30H" = "0" ] && [ "$RESULT_6H" != "0" ]; then
    echo "✅ 数据保留策略可能已生效"
elif [ "$RESULT_6H" = "0" ]; then
    echo "⚠️  可能没有日志数据，或日志还未到达"
else
    echo "❌ 数据保留策略可能未生效，或数据还未达到保留期限"
fi

# 8. 显示当前时间和建议
echo ""
echo "7. 状态总结..."
echo "当前时间: $(date)"
echo "数据目录大小: $(du -sh $LOKI_DATA_PATH/chunks | cut -f1)"
echo ""
echo "建议："
echo "1. 如果是刚刚重启的容器，等待 10-15 分钟让 compactor 开始工作"
echo "2. 继续监控数据目录大小变化"
echo "3. 检查是否有持续的日志流入"

# 9. 显示下次检查的建议时间
NEXT_CHECK=$(date -d '+2 hours' '+%Y-%m-%d %H:%M')
echo "建议下次检查时间: $NEXT_CHECK"
```


## grafana配置loki

![[Pasted image 20250609142224.png]]
![[Pasted image 20250609142858.png]]
